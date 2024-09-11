<template>
  <div id="userLayout">
    <div class="top"></div>
    <a-layout style="min-height: 100vh">
      <a-layout-header class="header">
        <a-space>
          <img src="../assets/oj-logo.png" class="logo" @click="redirect" />
          <div @click="redirect">在线判题系统</div>
        </a-space>
      </a-layout-header>
      <a-layout-content class="content">
        <router-view />
      </a-layout-content>
      <a-layout-footer class="footer">
        <a href="https://blog.csdn.net/m0_69435612?type=blog" target="_blank">
          喋喋不休&沉默不语
        </a>
      </a-layout-footer>
    </a-layout>
  </div>
</template>
<script setup lang="ts">
import { useRouter } from "vue-router";

const router = useRouter();
const redirect = () => {
  router.push("/");
};
</script>
<style scoped>
#userLayout {
  text-align: center;
  background: url("https://gw.alipayobjects.com/zos/rmsportal/FfdJeJRQWjEeGTpqgBKj.png")
    0% 0% / 100% 100%;
}

#userLayout .logo {
  width: 64px;
  height: 64px;
}

.top {
  margin-top: 16px;
  background-color: #fdfeff;
}

#userLayout .header {
  background-color: #fdfeff;
}

#userLayout .content {
  margin-bottom: 16px;
  padding: 20px;
  background-color: #fdfeff;
}

#userLayout .footer {
  padding: 16px;
  position: sticky;
  bottom: 0;
  left: 0;
  right: 0;
  text-align: center;
}
</style>
<script></script>
