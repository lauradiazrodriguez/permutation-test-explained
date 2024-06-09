<script>
    import { onMount } from 'svelte';
    import * as d3 from 'd3';

    let data = [];
    let pValueLine = null;

    let svg;

    onMount(() => {
        svg = select("#histogram")
        runPermutationTest(); // run initially
    });

    function runPermutationTest() {
        const mean = 50;  // Center of the data
        const deviation = 10;  // Spread of the data
        const sampleSize = 1000;  // Number of data points

        // Generate Gaussian-distributed data
        const randomNormal = d3.randomNormal(mean, deviation);
        data = Array.from({length: sampleSize}, randomNormal);

        // Simulate a test statistic, for example, the mean of a subset
        const testStatistic = d3.quantile(data.slice(0, 100), 0.75);

        // Calculate p-value directly based on data
        const pValue = data.filter(d => d >= testStatistic).length / data.length;
        const pValueThreshold = 0.05;  // You can adjust this threshold
        
        // Find the value in the data that corresponds to the top 5% (or your threshold)
        pValueLine = d3.quantile(data, 1 - pValueThreshold);

        // Update histogram on data change
        updateHistogram();
    }

    function updateHistogram() {
        const svg = d3.select("#histogram");
        svg.selectAll("*").remove();

        const margin = {top: 10, right: 30, bottom: 30, left: 40},
            width = 800 - margin.left - margin.right,
            height = 400 - margin.top - margin.bottom;

        // Set up x-scale
        const x = d3.scaleLinear()
            .domain([d3.min(data), d3.max(data)])
            .range([0, width]);

        // Generate bins for the histogram
        const histogramData = d3.histogram()
            .value(d => d)
            .domain(x.domain())
            .thresholds(x.ticks(40))(data);

        // Set up y-scale
        const y = d3.scaleLinear()
            .range([height, 0]);
        y.domain([0, d3.max(histogramData, d => d.length)]);

        // Draw bars
        const bars = svg.append("g")
            .attr("transform", `translate(${margin.left},${margin.top})`);

        bars.selectAll("rect")
            .data(histogramData)
            .enter().append("rect")
            .attr("x", 1)
            .attr("transform", d => `translate(${x(d.x0)},${y(d.length)})`)
            .attr("width", d => x(d.x1) - x(d.x0) - 1)
            .attr("height", d => height - y(d.length))
            .attr("fill", d => d.x0 >= pValueLine ? "#ef476f" : "#778da9");

        // Draw p-value line
        bars.append("line")
            .attr("x1", x(pValueLine))
            .attr("x2", x(pValueLine))
            .attr("y1", 0)
            .attr("y2", height)
            .attr("stroke", "#000000");

        const legend = svg.append("g")
        .attr("transform", `translate(${width - 60}, ${height + margin.top - 150})`); // Adjust position as needed

        legend.append("line")
          .attr("x1", 0)
          .attr("x2", 30)
          .attr("y1", -180)
          .attr("y2", -180)
          .attr("stroke", "#000000")

        legend.append("text")
          .attr("x", 40)
          .attr("y", -180)
          .text("Observed difference")
          .style("font-size", "12px")
          .attr("alignment-baseline", "middle");
    }

    
</script>

<svg id="histogram" width="850" height="400"></svg>
<button on:click={runPermutationTest}>Run Permutation Test</button>

<style>
  svg {
    margin: 20px auto;
    display: block;
    background-color: #f4f4f4;  /* Light background for the SVG */
    border-radius: 8px;  /* Rounded corners for the SVG */
    border: 1px solid #ccc;  /* Subtle border */
  }

  button {
    display: block;
    margin: 10px auto;
    padding: 10px 20px;
    color: white;
    background-color: #073b4c;  /* Bootstrap primary color */
    border: none;
    border-radius: 5px;
    cursor: pointer;
    font-size: 16px;
    box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
    transition: background-color 0.3s ease;
  }

  button:hover {
    background-color: #118ab2;  /* Darken button on hover */
  }

  #histogram .bar rect {
    fill: steelblue;  /* Change bar color to steelblue */
    transition: fill 0.3s ease;  /* Smooth transition for bar color change */
  }

  #histogram .bar rect:hover {
    fill: #b0c4de;  /* Lighter blue on hover */
  }
</style>