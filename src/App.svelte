<script>
  const tickDelay = 700; // ms
  const colCount = 60;
  const rowCount = 30;
  let timer = null;
  let running = false;
  let cells = Array(colCount * rowCount).fill({ alive: false });

  const setup = () => {
    const initial = [];
    const x = colCount * rowCount;
    for (let i = 0; i < x; i++) {
      initial.push({ alive: Math.random() > 0.5 });
    }
    return initial;
  };

  const livingNeighbours = index => {
    return [
      cells[index - 1], // left
      cells[index + 1], // right
      cells[index + colCount - 1], // bottom left
      cells[index + colCount], // bottom
      cells[index + colCount + 1], // bottom right
      cells[index - colCount - 1], // top left
      cells[index - colCount], // top
      cells[index - colCount + 1] // top right
    ].filter(c => c && c.alive);
  };

  const tick = () => {
    return cells.map((cell, index) => {
      if (livingNeighbours(index).length === 3) {
        return { alive: true };
      }
      if (cell.alive && livingNeighbours(index).length === 2) {
        return { alive: true };
      }
      return { alive: false };
    });
  };

  const stop = () => {
    running = false;

    if (timer) {
      clearTimeout(timer);
      timer = null;
    }
  };

  const run = () => {
    running = true;
    timer = setTimeout(() => {
      cells = tick();
      if (cells.some(c => c.alive)) {
        run();
      } else {
        stop();
      }
    }, tickDelay);
  };

  const reset = () => {
    cells = Array(colCount * rowCount).fill({ alive: false });
    stop();
  };

  const toggle = () => {
    if (!cells.some(c => c.alive)) {
      cells = setup();
    }

    if (running) {
      stop();
    } else {
      run();
    }
  };
</script>

<style>
  main {
    --gamesome-pink: #dd2d5a;
    --border-color: #d9d9d9;

    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
  }

  header {
    display: grid;
    grid-gap: 16px;
    grid-template-columns: 10fr 1fr 1fr;
    align-items: center;
    width: 100%;
    max-width: 1350px;
    padding: 8px 16px;
  }

  h1 {
    color: var(--gamesome-pink);
    font-weight: 100;
    margin: 0;
  }

  button {
    border: none;
    max-height: 32px;
    padding: 8px 16px;
    color: #fff;
    background: var(--gamesome-pink);
    display: flex;
    align-items: center;
    justify-content: center;
    border-radius: 20px;
    transition: filter 0.3s ease;
  }

  .game-grid {
    margin-top: 20px;
    display: grid;
    justify-items: center;
    grid-gap: 0.5px;
  }

  .cell {
    height: 20px;
    width: 20px;
    border: 1px solid var(--border-color);
  }
  .cell[data-status="alive"] {
    background: var(--gamesome-pink);
  }
  .cell[data-status="dead"] {
    background: transparent;
  }
</style>

<main>
  <header>
    <h1>Game of Life</h1>
    <button on:click={toggle}>{running ? 'Pause' : 'Start'}</button>
    <button on:click={reset}>Reset</button>
  </header>
  <div
    class="game-grid"
    style="grid-template-columns: repeat({colCount}, minmax(20px, 1fr));">
    {#each cells as cell}
      <div class="cell" data-status={cell.alive ? 'alive' : 'dead'} />
    {/each}
  </div>
</main>
