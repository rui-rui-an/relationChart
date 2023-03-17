<template>
  <div id="app">
    <div class="title">
      请双击节点进行分析
    </div>
    <router-view />
  </div>
</template>

<style lang="less">
#app {
  height: 100%;
  .title{
    height: 10vh;
    line-height: 10vh;
    font-size: 24px;
    font-weight: bold;
    text-align: center;
  }
}
</style>
