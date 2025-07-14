<script>
  import data from "$data/data.js";
  console.log(data);

  import { scaleLinear } from "d3-scale";
  import {fly} from "svelte/transition";


    const margin = {
    top: 20,
    right: 20, 
    bottom: 20, 
    left: 0
  }

  let width = 400;
  $: innerWidth = width - margin.left - margin.right;
  $: xScale = scaleLinear()
    .domain([0, 100]) // Input
    .range([0, innerWidth]); // Output

  let height = 400;
  let innerHeight = height - margin.top - margin.bottom;
  let yScale = scaleLinear()
    .domain([0, 60])
    .range([innerHeight, 0]);

  import AxisX from "$components/AxisX.svelte"
  import AxisY from "$components/AxisY.svelte"
  import Tooltip from "$components/Tooltip.svelte"

  let hoveredData;
  $: console.log(hoveredData);


</script>



<!-- Svelte {#each} block -->
<div class='chart-container' bind:clientWidth={width}>
    <h1>Students who studied for more hours scored higher on their final exam</h1>
  <svg {width} {height} 
          on:mouseleave={() => {
            hoveredData = null;
        }}
    >
    <g transform="translate({margin.left} {margin.top})">
    <AxisX {xScale} height={innerHeight} width={innerWidth}/>
    <AxisY {yScale} width={innerWidth}/>
    {#each data.sort((a, b) => a.grade - b.grade) as d}

      <circle
      in:fly={{x: -10, opacity: 0, duration: 500}}
        cx={xScale(d.grade)}
        cy={yScale(d.hours)}
        r={hoveredData === d ? 15 : 10}
        opacity={hoveredData ? (hoveredData === d ? 1 : 0.45) : 1}
        fill="purple"
        stroke="black"
        stroke-width="1"
        on:mouseover={() => {
            hoveredData = d;
        }}
        on:focus={() => {
            hoveredData = d;
        }}
        tabIndex="0"
      />
    {/each}
    </g>
  </svg>
  {#if hoveredData}
    <Tooltip data={hoveredData} {xScale} {yScale}
    width={innerWidth}/>
    
  {/if}
</div>

<style>
  :global(.tick text, .axis-title) {
    font-weight: 400;
    font-size: 12px;
    fill: #8f8f8f;
  }

  circle {
    transition: r 300ms ease, opacity 500ms ease;
    cursor: pointer;
  }

  h1 {
    font-size: 1.4rem;
    font-weight: 600;
    margin-bottom: 0.5rem;
  }

  .chart-container {
    position: relative;
    width: 100%;
    max-width: 600px;
    margin: 0 auto;
    margin-top: 50px;
  }
  

</style>
