<script>
  import { onMount } from 'svelte';
  import { select, scaleLinear, scaleBand, max, axisBottom, axisLeft } from 'd3';

  const dataHist = [
    { label: '1', value: 1 }, { label: '2', value: 3 }, { label: '3', value: 7 },
    { label: '4', value: 12 }, { label: '5', value: 20 }, { label: '6', value: 30 },
    { label: '7', value: 45 }, { label: '8', value: 50 }, { label: '9', value: 45 },
    { label: '10', value: 30 }, { label: '11', value: 20 }, { label: '12', value: 12 },
    { label: '13', value: 7 }, { label: '14', value: 3 }, { label: '15', value: 1 }
  ];

  let svg;

  onMount(() => {
    svg = select("#histogram");
    drawHistogram();
  });

  function drawHistogram() {
    svg.html(''); // Clear the SVG before redrawing
    const margin = { top: 10, right: 30, bottom: 30, left: 40 };
    const width = 1200 - margin.left - margin.right;
    const height = 300 - margin.top - margin.bottom;

    const xScale = scaleBand()
      .domain(dataHist.map(d => d.label))
      .range([0, width])
      .padding(0.1);

    const yScale = scaleLinear()
      .domain([0, max(dataHist, d => d.value)])
      .range([height, 0]);

    const g = svg.append('g')
      .attr('transform', `translate(${margin.left},${margin.top})`);

    g.selectAll(".bar")
      .data(dataHist)
      .enter().append("rect")
      .attr("class", "bar")
      .attr("x", d => xScale(d.label))
      .attr("y", d => yScale(d.value))
      .attr("width", xScale.bandwidth())
      .attr("height", d => height - yScale(d.value))
      .attr("fill", "steelblue");

    g.append("g")
      .attr("transform", `translate(0,${height})`)
      .call(axisBottom(xScale));

    g.append("g")
      .call(axisLeft(yScale));

    // P-value line
    const pValueCategory = '11'; // This should be a label from your `dataHist`
    const pValueXPosition = xScale(pValueCategory) + xScale.bandwidth() / 2; // Centers the line in the band

    g.append("line")
      .attr("x1", pValueXPosition)
      .attr("x2", pValueXPosition)
      .attr("y1", 0)
      .attr("y2", height)
      .attr("stroke", "red")
      .attr("stroke-dasharray", "5,5");

    const legend = svg.append("g")
      .attr("transform", `translate(${width - 160}, ${height + margin.top - 20})`); // Adjust position as needed

    legend.append("line")
      .attr("x1", 0)
      .attr("x2", 30)
      .attr("y1", -180)
      .attr("y2", -180)
      .attr("stroke", "red")
      .attr("stroke-dasharray", "5,5");

    legend.append("text")
      .attr("x", 40)
      .attr("y", -180)
      .text("Observed difference")
      .style("font-size", "12px")
      .attr("alignment-baseline", "middle");
  }
</script>

<section id="conclusion">
  <div class="columns">
    <p class="body-text">
      Next, you compare your observed difference in brilliance average to the 
      distribution of differences from your permutations. You count how many 
      times the permutation differences are equal to or greater than your 
      observed difference.
    </p>
    <p class="body-text">
      However, if a large number of permutations result in a difference 
      equal to the one you observed or more, the observed difference could easily 
      occur by random chance, and you might reconsider the effectiveness of the 
      new substance.
    </p>
    <p class="body-text">
      If only a small fraction of these permutations result in a difference 
      equal to the one you observed, you can conclude that the observed difference 
      is unlikely to have occurred by chance. This would suggest that the new 
      polishing substance genuinely enhances the brilliance of the diamonds.
    </p>
    <p class="body-text">
      By comparing the observed difference to this distribution, you make 
      an informed decision about the new polishing substance. If a small fraction 
      of these permutations result in a difference equal to the one you observed,
      you might adopt the new method, enhancing your reputation as a seller of 
      the most brilliant antique diamonds.
    </p>
  </div>
  <div class="hist-container">
    <svg id="histogram" width="1200" height="300"></svg>
    <button>Run Permutation Test</button>
  </div>
</section>

<style>
  #conclusion {
    padding: 2rem 6rem;
  }
  .columns {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 1.5rem;
  }
  .body-text {
    margin: 0;
  }
  .hist-container {
    padding: 5rem 1rem;
  }
</style>