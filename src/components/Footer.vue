<template>
  <div class="footer">
    Copyright © 2016-2020 All Rights Reserved
  </div>
</template>

<script>
  export default {
    name: 'footer',
  }
</script>

<style scoped>

  .footer{
    position: fixed;
    bottom: 0;
    left: 0;
    right: 0;
    z-index: 100;
    width: 100%;
    background: #f3f3f3;
    height: 30px;
    line-height: 30px;
    text-align: center;
    color: #797979;
    border-top: 1px solid #ddd;
  }
</style>
