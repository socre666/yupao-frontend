<template>
  <template v-if="user">
    <van-cell title="当前用户" :value="user?.username" />
    <van-cell title="修改信息" is-link to="/user/update" />
    <van-cell title="我创建的队伍" is-link to="/user/team/create" />
    <van-cell title="我加入的队伍" is-link to="/user/team/join" />
    <van-button type="primary" size="large" @click="logout">退出登录</van-button>

  </template>
</template>

<script setup lang="ts">
import {useRouter} from "vue-router";
import {onMounted, ref} from "vue";
import myAxios from "../plugins/myAxios";
import {Dialog, Toast} from "vant";
import {getCurrentUser} from "../services/user";


const user = ref();

const router = useRouter();

onMounted(async () => {
  user.value = await getCurrentUser();
})

const toEdit = (editKey: string, editName: string, currentValue: string) => {
  router.push({
    path: '/user/edit',
    query: {
      editKey,
      editName,
      currentValue,
    }
  })
}
const logout = () =>{

  Dialog.confirm({
    message: '你是否要退出登录',
  })
      .then(async () => {
        const res = await myAxios.post('/user/logout')
        if (res.code === 0 && res.data) {
        Toast.success('退出成功');
        router.push('/user/login')
        } else {
        Toast.fail('退出失败');
        }
        // on confirm
      })
      .catch(() => {
        // on cancel
      });
}
</script>

<style scoped>
</style>
