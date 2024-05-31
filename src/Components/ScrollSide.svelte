<script lang="ts"> 
  import Scrolly from "./Scrolly.svelte";
  import katexify from "../katexify";
  import { select, selectAll } from "d3-selection";
  import { fly, draw } from "svelte/transition";
  import { tweened } from "svelte/motion";

  // scroll iterator
  let value;

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
        <br><br>After polishing, you evaluate the brilliance of each diamond, 
        assigning a score based on their sparkle and shine.
      </p>`,

    `<h1 class='step-title'>Observing the Results</h1>
      <p>
        The average brilliance score for diamonds polished with your usual method is 50, 
        and for those polished with the new substance, it's 55. 
        <br><br>The new substance seems promising, but you need to determine if 
        this difference is genuinely due to the polishing substance or if it could have occurred by chance.
      </p>`,

    `<h1 class='step-title'>The Permutation Test</h1>
    <p>
      To address this, you decide to use a permutation test. This test will help
       you determine whether the observed difference in brilliance scores is 
       statistically significant.
    </p>`,

    `<h1 class='step-title'>Step 1: Combining and Shuffling</h1>
    <p>
      First, you combine all the brilliance scores into a single pool, 
      disregarding which polishing method was used. Then, you randomly 
      shuffle, which we call permuting, these scores and split them into 
      two new groups of 10 diamonds each.
    </p>`,

    `<h1 class='step-title'>Step 2: Calculating the Difference</h1>
    <p>
      For each permutation, you calculate the difference in average brilliance 
      scores between the two new groups. This process is repeated many times 
      (e.g., 10,000 permutations) to build a distribution of differences under 
      the null hypothesis—that the polishing method doesn't affect the brilliance.
    </p>`,

    `<h1 class='step-title'>Step 3: Comparing the Observed Difference</h1>
    <p>
      Next, you compare your observed difference (55 - 50 = 5) to the 
      distribution of differences from your permutations. You count how many 
      times the permutation differences are equal to or greater than your 
      observed difference.
    </p>`,

    `<h1 class='step-title'>Calculating the p-Value</h1>
     <p>
      The p-value represents the probability of obtaining an observed difference as extreme as, or more extreme than, what was actually observed, assuming the null hypothesis is true. In this context, it's the fraction of permutations where the difference in brilliance scores is 5 or more.
      <br><br>For example, if only 100 out of 10,000 permutations result in a difference of 5 or more, the p-value would be 100/10,000 = 0.01.

    </p>`,

    `<h1 class='step-title'>Interpreting the Results</h1>
      <p>
        If only a small fraction of these permutations result in a difference of 5 or more, you can conclude that the observed difference is unlikely to have occurred by chance. This would suggest that the new polishing substance genuinely enhances the brilliance of the diamonds.
        <br><br>However, if a large number of permutations result in a difference of 5 or more, the observed difference could easily occur by random chance, and you might reconsider the effectiveness of the new substance.
        <br><br>We measure this statistically by using the p-value. If the p-value is very small (typically less than 0.05), it suggests that the observed difference is unlikely to have occurred by chance, indicating that the new polishing substance has a significant effect on the brilliance of the diamonds. However, if the p-value is large, it suggests that the observed difference could easily occur by random chance.
    </p>`,

  `<h1 class='step-title'>Decision Making</h1>
    <p>
      By comparing the observed difference to this distribution and calculating the p-value, you make an informed decision about the new polishing substance. Since the p-value is less than 0.05, you might adopt the new method, enhancing your reputation as a seller of the most brilliant antique diamonds.
    </p>
    `,
  ];

  const target2event = {
    0: () => {
      const svg = select("#chart1")

      const diamondPositions = [];
      const diamondSize = 40; // Adjust the size if needed
      const radius = diamondSize / 2;

      for (let i = 0; i < 20; i++) {
        let xOffset, yOffset, isOverlap;

        do {
          isOverlap = false;
          // Random position around (250, 250) within a range
          xOffset = 250 + (Math.random() * 200 - 100);
          yOffset = 250 + (Math.random() * 200 - 100);

          // Check for overlap with existing diamonds
          for (let pos of diamondPositions) {
            const dx = xOffset - pos[0];
            const dy = yOffset - pos[1];
            const distance = Math.sqrt(dx * dx + dy * dy);

            if (distance < diamondSize) {
              isOverlap = true;
              break;
            }
          }
        } while (isOverlap);

        diamondPositions.push([xOffset, yOffset]);

        svg.append("path")
          .attr("d", "M17 20, L40 20, L50 40, L27 60, L5 40, Z M5 40, L50 40, M27 60, L40 20, M27 60, L17 20, M22 40, L28 21, L33 40")
          .attr("transform", `translate(${xOffset}, ${yOffset}) scale(0.5)`)
          .attr("style", "fill:lightblue; stroke:grey; stroke-width:1");
      }

      select("#chart1").style("background-color", "violet");

    },

    1: () => {
      select("#chart1").style("background-color", "red");
    },

    2: () => {
      select("#chart1").style("background-color", "purple");
    },

    3: () => {
      select("#chart1").style("background-color", "violet");
    },

    4: () => {
      select("#chart1").style("background-color", "red");
    },

    5: () => {
      select("#chart1").style("background-color", "purple");
    },

    6: () => {
      select("#chart1").style("background-color", "violet");
    },

    7: () => {
      select("#chart1").style("background-color", "red");
    },

    8: () => {
      select("#chart1").style("background-color", "purple");
    },

    9: () => {
      select("#chart1").style("background-color", "violet");
    },

  };

  $: if (typeof value !== "undefined") target2event[value]();
</script>

<section>
  <!-- scroll container -->
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
      <div class="chart-one">
        <svg id="chart1" />
      </div>
    </div>
  </div>
  <br /><br />

</section>

<style>
  #chart1,
  #chart2 {
    width: 100%;
    height: 100%;
  }
  .chart-one {
    width: 100%;
    height: 100%;
    border: 3px solid skyblue;
  }
  .chart-two {
    width: 100%;
    height: 100%;
    border: 3px solid coral;
  }
  /* space after scroll is finished */
  .spacer {
    height: 40vh;
  }

  .charts-container {
    position: sticky;
    top: 10%;
    display: grid;
    width: 50%;
    grid-template-columns: 100%;
    height: 85vh;
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

  /* Comment out the following line to always make it 'text-on-top' */
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
      height: 100vh;
    }
  }
</style>
