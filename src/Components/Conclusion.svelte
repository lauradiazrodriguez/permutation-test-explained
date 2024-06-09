<script>
    import Histogram from "/Users/lauradiaz/Desktop/DSC106_Final_Proj_Temp/src/Components/Histogram.svelte"
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
    <h1 class=hist-title>Permutation Test Histogram Generator</h1>
    <h3 class=hist-subtitle>The pink shaded region represents the probability of obtaining an observed difference as extreme as, or more extreme than, what was actually observed, assuming the null hypothesis is true. In this context, it's the fraction of permutations where the difference in brilliance scores is 5 or more.</h3>
    <Histogram></Histogram>
  </div>
  <div>
    <p class="caveat-text">
      This was not an exhaustive treatment of permutation testing, some things were left out. 
      <br>But we hope it was helpful in explaining the permutation test.
    </p>
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
  

  .hist-title,
  .hist-subtitle,
  .caveat-text {
    text-align: center;
  }


</style>