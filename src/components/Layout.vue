<template>
  <div class="layout-demo">
    <a-layout class="layout-wrapper">
      <a-layout-header class="header">
      </a-layout-header>
      <a-layout-content class="content">
        <router-view />
      </a-layout-content>
      <a-layout-footer class="footer">
      </a-layout-footer>
    </a-layout>
  </div>
</template>

<style scoped>
.layout-wrapper {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

.header {
  /* position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  z-index: 1000; */
}

.content {
  flex: 1;
  /* 让内容部分填充剩余空间 */
  overflow: auto;
}

.footer {
  /* background-color: #262626;
  color: #ffffff;
  padding: 40px;
  font-family: Arial, sans-serif; */
}
</style>

<script setup>
</script>
