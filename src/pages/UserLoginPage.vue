<template>
  <van-image
      round
      width="10rem"
      height="10rem"
      :src="logo"
      style="display: block;margin-left: auto;margin-right: auto"
  />
  <div style="text-align: center; padding: 20px;" >伙伴匹配系统</div>
  <van-form @submit="onSubmit">
    <van-cell-group inset>
      <van-field
          v-model="userAccount"
          name="userAccount"
          label="账号"
          placeholder="请输入账号"
          minlength = 5
          onkeyup="this.value=this.value.replace(/\s+/g,'')"
          :rules="[
                    {
                        required: true,
                        trigger:'onBlur',
                        message: '不能为空'
                    },
                    {
                        pattern: /^[a-zA-Z][a-zA-Z0-9_]{4,14}$/,
                        message: '账号字母开头,长度为5-15！！',
                        trigger: 'onBlur'
                    }
                ]"
      />
      <van-field
          v-model="userPassword"
          type="password"
          name="userPassword"
          label="密码"
          placeholder="请输入密码"
          minlength = 8
          onkeyup="this.value=this.value.replace(/\s+/g,'')"
          :rules="[
                    {
                        required: true,
                        trigger:'onBlur',
                        message: '不能为空'
                    },
                    {
                        pattern: /^.{8,20}$/,
                        message: '密码8-20位',
                        trigger: 'onBlur'
                    }
                ]"
      />
    </van-cell-group>
    <div style="margin: 16px;">
      <van-button round block type="primary" native-type="submit">
        登录
      </van-button>
    </div>
  </van-form>
  <div class="register" style="float: right">
    <span>没有账户?</span> <router-link class="a_router" to="/user/register">去注册</router-link>
  </div>
  <Icp />
</template>

<script setup lang="ts">
import {useRoute, useRouter} from "vue-router";
import {ref} from "vue";
import myAxios from "../plugins/myAxios";
import {Toast} from "vant";
import Icp from "../icp/Icp.vue";
import logo from  "../assets/logo.jpg"
const router = useRouter();
const route = useRoute();

const userAccount = ref('');
const userPassword = ref('');

const onSubmit = async () => {
  const res = await myAxios.post('/user/login', {
    userAccount: userAccount.value,
    userPassword: userPassword.value,
  })
  console.log(res, '用户登录');
  if (res.code === 0 && res.data) {
    Toast.success('登录成功');
    // 跳转到之前的页面，没有则跳到index页
    const redirectUrl = route.query?.redirect as string ?? '/home';
    window.location.href = redirectUrl;

  } else {
    Toast.fail('登录失败');
  }
};

</script>

<style scoped>

</style>
