<template>
  <a-row id="globalHeader" align="center" :wrap="false">
    <a-col flex="auto">
      <a-menu
        mode="horizontal"
        :selected-keys="selectedKeys"
        @menu-item-click="doMenuClick"
      >
        <a-menu-item
          key="0"
          :style="{ padding: 0, marginRight: '38px' }"
          disabled
        >
          <div class="title-bar">
            <img class="logo" src="../assets/oj-logo.png" @click="redirect" />
            <div class="title" @click="redirect">在线判题系统</div>
          </div>
        </a-menu-item>
        <a-menu-item v-for="item in visibleRoutes" :key="item.path">
          {{ item.name }}
        </a-menu-item>
      </a-menu>
    </a-col>
    <a-col flex="100px">
      <a-button v-if="!isLoggedIn" @click="navigateToLogin">
        {{
          store.state.user?.loginUser?.userName ??
          store.state.user?.loginUser?.userRole
        }}
      </a-button>
      <a-trigger>
        <a-button v-if="isLoggedIn">
          {{
            store.state.user?.loginUser?.userName ??
            store.state.user?.loginUser?.userRole
          }}
        </a-button>
        <template #content v-if="isLoggedIn">
          <div class="trigger-user">
            <a-button @click="logOut">退出登录</a-button>
          </div>
        </template>
      </a-trigger>
    </a-col>
  </a-row>
</template>

<script setup lang="ts">
import { routes } from "../router/routes";
import { useRoute, useRouter } from "vue-router";
import { computed, onMounted, ref, watch, watchEffect } from "vue";
import { StoreOptions, useStore } from "vuex";
import checkAccess from "@/access/checkAccess";
import ACCESS_ENUM from "@/access/accessEnum";
import { UserControllerService } from "../../generated";
import message from "@arco-design/web-vue/es/message";

const router = useRouter();
const store = useStore();
const isLoggedIn = ref(false);
const loginUser = ref();
const logout = ref();
/**
 * 检查用户是否登录（获取用户等录信息）
 */
const fetchLoginUser = async () => {
  const res = await UserControllerService.getLoginUserUsingGet();
  if (res.code === 0) {
    // loginUser.value = res.data;
    isLoggedIn.value = true;
  }
};
onMounted(() => {
  fetchLoginUser();
});
// 没登录的话点击登录
const navigateToLogin = () => {
  router.push("/user/login");
};
//退出登录
const logOut = async () => {
  const res = await UserControllerService.userLogoutUsingPost();
  if (res.code === 0) {
    // logout.value = res.data;
    isLoggedIn.value = false;
    message.success("注销成功");
    router.push("/user/login");
  } else {
    message.error(res.message as string);
  }
};
// onMounted(() => {
//   logOut();
// });
const redirect = () => {
  // 清除本地存储中的认证信息
  router.push("/");
};

// 展示在菜单的路由数组
const visibleRoutes = computed(() => {
  return routes.filter((item, index) => {
    if (item.meta?.hideInMenu) {
      return false;
    }
    // 根据权限过滤菜单
    if (
      !checkAccess(store.state.user.loginUser, item?.meta?.access as string)
    ) {
      return false;
    }
    return true;
  });
});

// 默认主页
const selectedKeys = ref(["/"]);

// 路由跳转后，更新选中的菜单项
router.afterEach((to, from, failure) => {
  selectedKeys.value = [to.path];
});

// console.log();

setTimeout(() => {
  store.dispatch("user/getLoginUser", {
    userName: "管理员",
    userRole: ACCESS_ENUM.ADMIN,
  });
}, 3000);

const doMenuClick = (key: string) => {
  router.push({
    path: key,
  });
};
</script>

<style scoped>
.trigger-user {
  padding: 10px;
  width: 100px;
  border-radius: 4px;
  //box-shadow: 0 2px 8px 0 rgba(0, 0, 0, 0.15);
}

.title-bar {
  display: flex;
  align-items: center;
}

.title {
  color: #444;
  margin-left: 16px;
}

.logo {
  height: 48px;
}
</style>
