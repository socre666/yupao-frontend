<template>
  <van-nav-bar
      title="注册"
      left-arrow
      @click-left="onClickLeft"
  >
  </van-nav-bar>
  <van-image
      round
      width="10rem"
      height="10rem"
      :src="logo"
      style="display: block;margin-left: auto;margin-right: auto"
  />
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
      <van-field
          v-model="checkPassword"
          type="password"
          name="checkPassword"
          label="确认密码"
          placeholder="请再次输入密码"
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
                        message: '确认密码一致',
                        trigger: 'onBlur'
                    }
                ]"
      />
    </van-cell-group>
    <div style="margin: 16px;">
      <van-button round block type="primary" native-type="submit">
        注册
      </van-button>
    </div>
  </van-form>
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
const checkPassword = ref('');

const onClickLeft = () => {
  router.back();
};

const onSubmit = async () => {
  if(userPassword.value ==checkPassword.value){
    const res = await myAxios.post('/user/register', {
      userAccount: userAccount.value,
      userPassword: userPassword.value,
      checkPassword: checkPassword.value,
    })

    console.log(res, '注册');
    if (res.code === 0 && res.data) {
      Toast.success('注册成功');
      router.push('/user/login')
    } else {
      Toast.fail('注册失败');
    }
  }else {
    Toast.fail("两次密码输入不一致")
  }

};

</script>

<style scoped>

</style>
