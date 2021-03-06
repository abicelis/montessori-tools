<script>
    import SpawnSlice from "../components/fractions/SpawnSlice.svelte";
    import Line from "../components/fractions/Line.svelte";
    import DragArea from '../components/library/DragArea.svelte';
    import NumberCard from '../components/checkerboard/NumberCard.svelte';
    import SpawnArea from "../components/library/SpawnArea.svelte";

    const sweepAudio = typeof Audio !== 'undefined' && new Audio('/audio/sweep.mp3');

    let fractions = [1, 1 / 2, 1 / 3, 1 / 4, 1 / 5, 1 / 6, 1 / 7, 1 / 8, 1 / 9, 1 / 10];
    let cards = ['1', '2', '3', '4', '5', '6', '7', '8', '9', '10', '+', '-', '×', '÷', '='];

    let draggables = [];
    let latest;

    let id = 0;
    const getId = () => id++;
    const onSpawn = (event) => {
        const {component, data} = event.detail;
        const id = getId();
        latest = id;
        draggables.push({id, component, data});
        draggables = draggables;
    };

    const emptyTrash = () => {
        draggables = [];
        sweepAudio && sweepAudio.play();
    }
</script>

<svelte:head>
  <title>Fractions - Virtual Montessori Materials</title>
</svelte:head>

<style>
    :global(div#fractions .fraction-size) {
        height: 100px;
        width: 100px;
    }
    div#fractions {
        position: relative;
        height: 100vh;
        width: 100vw;
        min-height: 100vh;
        min-width: 100vw;
        background: url(/img/table-top.jpg);
    }
    #slices {
        line-height: 0;
        display: flex;
        flex-wrap: wrap;
        border-bottom: 5px solid #945722;
        box-shadow: 0px 9px 10px rgba(0, 0, 0, .2);
    }
    .slice-box {
        position: relative;
        min-width: 20%;
        min-height: 150px;
        box-sizing: border-box;
        padding: 15px;
        flex-grow: 1;
        display: inline-flex;
        align-items: center;
        justify-content: center;
        border: 1px solid #945722;
        background: radial-gradient(circle at 50% 50%, rgb(45, 152, 37), rgb(26, 111, 26) 48%);
    }

    #drawer {
        display: flex;
        flex-wrap: wrap;
        padding: 15px;
        padding-top: 0px;
        background: #ffdab9;
        border-bottom: 5px solid #945722;
        justify-content: center;
        align-items: center;
        margin-bottom: 40vh;
    }
    :global(div#fractions #drawer > *:not(:last-child)) {
        margin-right: 15px;
    }

    :global(div#fractions #drawer > *) {
        margin-top: 15px;
    }

    #trash {
        position: fixed;
        height: 65px;
        width: 65px;
        right: 0;
        bottom: 0;
        background: #ffdab9;
        padding: 8px;
        border-top-left-radius: 15%;
    }
    img {
        max-width: 100%;
        max-height: 100%;
    }
</style>

<div id="fractions">
    <DragArea>
        <div id="slices">
            {#each fractions as fraction, index}
                <div class="slice-box fraction-size">
                    {#each Array(index + 1) as j, offset}
                        <SpawnSlice fraction={fraction} offset={offset} on:spawn={onSpawn} />
                    {/each}
                </div>
            {/each}
        </div>
        <div id="drawer">
            {#each cards as card}
                <SpawnArea on:spawn={onSpawn} component={NumberCard} data={{number: card}}>
                    <NumberCard disabled="{true}" number={card} />
                </SpawnArea>
            {/each}
            <SpawnArea on:spawn={onSpawn} component={Line}>
                <Line disabled={true} />
            </SpawnArea>
        </div>

        <button on:click={emptyTrash} id="trash">
            <img src="/icons/recycle.svg" alt="Clear" />
        </button>

        {#each draggables as draggable (draggable.id)}
            <svelte:component this="{draggable.component}" {...draggable.data}
                              startPickedUp={draggable.id === latest}
                              index={draggable.id} />
        {/each}
    </DragArea>
</div>
