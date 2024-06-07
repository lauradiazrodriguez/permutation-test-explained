<script>
  import Scrolly from "./Scrolly.svelte";
  import { select, selectAll } from "d3-selection";
  import { scaleLinear } from "d3-scale";
  import { tweened } from 'svelte/motion';
  import { cubicOut } from 'svelte/easing';

  // find width of svg and use it
  let xScale = scaleLinear().domain([0, 396]).range([0, 511]);

  let data = [
    {x: 250, y: 50},
    {x: 345, y: 82},
    {x: 415, y: 150},
    {x: 450, y: 245},
    {x: 415, y: 340},
    {x: 345, y: 408},
    {x: 250, y: 470},
    {x: 155, y: 408},
    {x: 85, y: 340},
    {x: 50, y: 245},
    {x: 85, y: 150},
    {x: 155, y: 82},
    {x: 250, y: 120},
    {x: 320, y: 180},
    {x: 380, y: 250},
    {x: 320, y: 320},
    {x: 250, y: 400},
    {x: 180, y: 320},
    {x: 120, y: 250},
    {x: 180, y: 180}
  ];

  let twoCoords = [
    // Group 1
    { x: 50, y: 80 },
    { x: 130, y: 180 },
    { x: 130, y: 100 },
    { x: 70, y: 200 },
    { x: 110, y: 250 },
    { x: 50, y: 300 },
    { x: 90, y: 350 },
    { x: 130, y: 400 },
    { x: 70, y: 450 },
    { x: 110, y: 500 },

    // Group 2
    { x: 350, y: 80 },
    { x: 390, y: 150 },
    { x: 430, y: 100 },
    { x: 350, y: 200 },
    { x: 410, y: 250 },
    { x: 350, y: 300 },
    { x: 390, y: 350 },
    { x: 430, y: 400 },
    { x: 370, y: 450 },
    { x: 410, y: 500 }
  ];

  let currentCoords = data;

  // Create tweened stores for each coordinate
  //tweenedCoords = currentCoords.map(coord => ({
  //  x: tweened(coord.x, { duration: 1000, easing: cubicOut }),
  //  y: tweened(coord.y, { duration: 1000, easing: cubicOut })
  //}));

  //tweenedCoords = tweened(currentCoords, {
  //  duration: 500,
  //  easing: linear,
  // });

  let value = 0;

  // helper for permuteDiamonds
  //function shuffleArray(array) {
  //  for (let i = array.length - 1; i > 0; i--) {
  //    const j = Math.floor(Math.random() * (i + 1));
  //    [array[i], array[j]] = [array[j], array[i]];
  //  }
  //  return array;
  //}

  //function permuteDiamonds(coords) {
  //  const halfLength = Math.ceil(coords.length / 2);
  //  const group1 = coords.slice(0, halfLength);
  //  const group2 = coords.slice(halfLength);

  //  const shuffledGroup1 = shuffleArray(group1.map(coord => ({ ...coord })));
  //  const shuffledGroup2 = shuffleArray(group2.map(coord => ({ ...coord })));

  //  return [...shuffledGroup1, ...shuffledGroup2];
  //}

  function shuffleArray(array) {
    for (let i = array.length - 1; i > 0; i--) {
      const j = Math.floor(Math.random() * (i + 1));
      [array[i], array[j]] = [array[j], array[i]];
    }
    return array;
  }

  function permuteDiamonds(coords) {
    return shuffleArray(coords.map(coord => ({ ...coord })));
  }

  // drawing diamonds
  function drawDiamonds() {
    const svg = select("#chart1");
    const brilliance_score = [54, 55, 56, 54, 55, 56, 54, 55, 56, 55, 50, 55, 52, 49, 43, 50, 54, 51, 46, 50]
    svg.selectAll("*").remove(); // Clear previous SVG elements to avoid drawing again

    data.forEach((diamond, index) => {
      const pos = [diamond.x, diamond.y];
      const g = svg.append('g').attr('class', 'diamond').attr('transform', `translate(${pos[0]}, ${pos[1]}) `)

      g.append('path')
        .attr('class', 'diamond-path')
        .attr('x', diamond.x)
        .attr('y', diamond.y)
        .attr('d', "M17 20, L40 20, L50 40, L27 60, L5 40, Z M5 40, L50 40, M27 60, L40 20, M27 60, L17 20, M22 40, L28 21, L33 40")
        .attr('transform', `scale(1)`)
        .attr('style', `fill:lightblue; stroke:grey; stroke-width:1`); 

      const label = g.append('text')
      label.text(brilliance_score[index]).attr('dy', 80).attr('dx', 12).attr('style', `fill:grey`);
    });
    }

  const tweenedCoords = tweened(data, { duration: 400, easing: cubicOut });

  function updateDiamonds(newCoords) {
    tweenedCoords.set(newCoords);
  }

  $: currentCoords = $tweenedCoords;

  $: if (currentCoords) {
    const svg = select("#chart1");
    svg.selectAll('.diamond')
      .data(currentCoords)
      .attr('transform', (d, i) => `translate(${currentCoords[i].x}, ${currentCoords[i].y})`);
  }

  //function updateDiamonds(newCoords) {
    // redraw dataset
  //  currentCoords = newCoords;
  //  selectAll('.diamond')
  //  //  currentCoords.forEach((newCoord, i) => {
  //  //  tweenedCoords[i].x.set(newCoord.x);
  //  //  tweenedCoords[i].y.set(newCoord.y);});
  //  .attr('transform', (d, i) => `translate(${currentCoords[i]['x']}, ${currentCoords[i]['y']}) scale(0.95)`);
  //}

  // function to make random positins

  // Paragraph text for scrolly
  $: steps = [
      `<p>
        Imagine you're an antique jewelry seller specializing in rare and vintage 
        diamonds. One day, you're approached by a supplier offering a new polishing 
        substance that promises to enhance the brilliance of old diamonds. You're 
        intrigued but skeptical. To make an informed decision, you decide to conduct
        an experiment using statistics.
        <br><br>In statistical testing, we structure experiments in terms of null & 
        alternative hypotheses. Your test will have the following hypothesis:<br><br>
        &Eta;<sub>0</sub>: &mu;<sub>treatment</sub> <= &mu;<sub>control</sub>
        <br>
        &Eta;<sub>A</sub>: &mu;<sub>treatment</sub> >  &mu;<sub>control</sub>
        <br><br>Your null hypothesis claims that the new polishing substance does not increase the brilliance of old diamonds. The alternative hypothesis claims the opposite; new polishing substance yields superior diamond shine. 
      </p>`,

    `<h1 class='step-title'>Setting the Scene</h1>
      <p>
        In your collection, you have 20 vintage diamonds. You randomly split them 
        into two groups:
        <br><br>Group A: 10 diamonds to be polished with your usual method.
        <br><br>Group B: 10 diamonds to be polished with the new substance.
        <br><br> The average brilliance score for diamonds polished with your usual method is lower, 
        than those polished with the new substance.
        <br><br>The new substance seems promising, but you need to determine if 
        this difference is genuinely due to the polishing substance or if it could have occurred by chance.
      </p>`,

    `<h1 class='step-title'>The Permutation Test</h1>
    <p>
      First, you combine all the brilliance scores into a single pool, 
      disregarding which polishing method was used. Then, you randomly 
      shuffle, which we call permuting, these scores and split them into 
      two new groups of 10 diamonds each.
    </p>`
  ];

  const target2event = {
    0: () => {
      drawDiamonds();
    },
    1: () => {
      updateDiamonds(twoCoords);
    },
    2: () => {
      updateDiamonds(permuteDiamonds(currentCoords));
    }
  }

  // Reactive function to redraw diamonds when scrolling
  $: if (value !== undefined) {
    target2event[value]?.();
  
  }

  
</script>

<section>
  <div class="section-container">
    <div class="steps-container">
      <Scrolly bind:value>
        {#each steps as text, i}
          <div class="step" class:active={value === i}>
            <div class="step-content">{@html text}</div>
          </div>
        {/each}
        <div class="spacer" />
      </Scrolly>
    </div>
    <div class="charts-container">
      <svg id="chart1">
    </div>
  </div>
</section>

<style>
  #chart1 {
    width: 100%;
    height: 100%;
  }
  .chart-one {
    width: 100%;
    height: 100%;
    border: 3px solid skyblue;
  }
  .charts-container {
    position: sticky;
    top: 10%;
    display: grid;
    width: 50%;
    grid-template-columns: 100%;
    height: 80vh;
    border: 0px;
  }

  .section-container {
    margin-top: 1em;
    text-align: center;
    transition: background 100ms;
    display: flex;
  }

  .step {
    height: 110vh;
    display: flex;
    place-items: center;
    justify-content: center;
  }

  .step-content {
    font-size: 18px;
    background: var(--bg);
    color: #ccc; 
    padding: 1rem 1rem;
    display: flex;
    flex-direction: column;
    justify-content: center;
    transition: background 400ms ease;
    text-align: left;
    width: 75%;
    margin: auto;
    max-width: 500px;
    font-family: var(--font-main);
    line-height: 1.3;
    border: 0px;
  }

  .step.active .step-content {
    background: #f1f3f3ee;
    color: var(--squid-ink);
  }

  .steps-container {
    height: 100%;
  }

  .steps-container {
    flex: 1 1 40%;
    z-index: 10;
  }

  /* Responsive adjustments for smaller screens */
  @media screen and (max-width: 950px) {
    .section-container {
      flex-direction: column-reverse;
    }

    .steps-container {
      pointer-events: none;
    }

    .charts-container {
      top: 7.5%;
      width: 95%;
      margin: auto;
    }

    .step {
      height: 130vh;
    }

    .step-content {
      width: 95%;
      max-width: 768px;
      font-size: 17px;
      line-height: 1.6;
    }

    .spacer {
      height: 80vh;
    }
  }
</style>
