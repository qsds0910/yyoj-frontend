<template>
  <div id="userRegisterView">
    <h2 style="margin-bottom: 16px">用户注册</h2>
    <a-form
      style="max-width: 480px; margin: 0 auto"
      label-align="left"
      auto-label-width
      :model="form"
      @submit="handleSubmit"
    >
      <a-form-item field="userAccount" label="账号">
        <a-input v-model="form.userAccount" placeholder="请输入账号" />
      </a-form-item>
      <a-form-item field="userPassword" tooltip="密码不少于 8 位" label="密码">
        <a-input-password
          v-model="form.userPassword"
          placeholder="请输入密码"
        />
      </a-form-item>
      <a-form-item
        field="checkPassword"
        tooltip="密码不少于 8 位"
        label="再次确认密码："
      >
        <a-input-password
          v-model="form.checkPassword"
          placeholder="请输入密码"
        />
      </a-form-item>
      <a-form-item>
        <a-button type="primary" html-type="submit" style="width: 120px">
          注册
        </a-button>
      </a-form-item>
    </a-form>
  </div>
</template>

<script setup lang="ts">
import { reactive } from "vue";
import { UserControllerService, UserRegisterRequest } from "../../../generated";
import message from "@arco-design/web-vue/es/message";
import { useRouter } from "vue-router";
import { useStore } from "vuex";

/**
 * 表单信息
 */
const form = reactive({
  userAccount: "",
  userPassword: "",
  checkPassword: "",
} as UserRegisterRequest);

const router = useRouter();
const store = useStore();
/**
 *睡一会
 */
function sleep(ms: number | undefined) {
  return new Promise((resolve) => setTimeout(resolve, ms));
}

/**
 * 提交表单
 * @param data
 */
const handleSubmit = async () => {
  if (form.checkPassword === form.userPassword) {
    const res = await UserControllerService.userRegisterUsingPost(form);
    // 注册成功，跳转到登录页
    if (res.code === 0) {
      message.success("注册成功，已为您自动跳转登录页");
      await sleep(1000);
      await store.dispatch("user/getRegister");
      router.push({
        path: "/user/login",
        replace: true,
      });
    } else {
      message.error("注册失败，" + res.message);
    }
    //todo 密码对比
  } else {
    message.error("两次输入的密码不一致，请重新输入");
  }
};
</script>
