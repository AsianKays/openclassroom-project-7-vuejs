<template>
  <svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
       viewBox="0 0 501.28 501.28" style="enable-background:new 0 0 501.28 501.28;" xml:space="preserve" width="25px" height="25px">
    <g :class="format">
      <polygon class="part-two" points="501.28,194.37 335.26,159.33 250.64,12.27 250.64,419.77 405.54,489.01 387.56,320.29 	"/>
      <polygon class="part-one" points="166.02,159.33 0,194.37 113.72,320.29 95.74,489.01 250.64,419.77 250.64,12.27 	"/>
    </g>
  </svg>
</template>

<script>
  export default {
    props: {
      format: {
        type: String,
        default: "full"
      }
    }
  }
</script>

<style lang="scss">
  // Full star
  .full {
    .part-one{
      fill: #FFDA44;
    }
    .part-two{
      fill: #FFCD00;
    }
  }

  // Half star
  .half {
    .part-one {
      fill: #FFDA44;
    }
    .part-two {
      fill: #d3d3d3;
    }
  }

  // Empty star
  .empty {
    .part-one {
      fill: #d3d3d3;
    }
    .part-two {
      fill: #d3d3d3;
    }
  }
</style>
