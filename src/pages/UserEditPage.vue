<template>
  <van-form @submit="onSubmit" v-if="editUser.editName=== '用户头像'">
    <van-uploader v-model="fileList" multiple :max-count="1" :before-read="beforeRead" :after-read="afterRead" />
    <div style="margin: 16px;">
      <van-button round block type="primary" native-type="submit">
        修改
      </van-button>
    </div>
  </van-form>
  <van-form @submit="onSubmit" v-if="editUser.editName==='性别'">
      <van-radio-group v-model="checked" direction="horizontal" @change="checkedChange">
        <van-radio name="男">男</van-radio>
        <van-radio name="女">女</van-radio>
      </van-radio-group>
    <div style="margin: 16px;">
      <van-button round block type="primary" native-type="submit">
        修改
      </van-button>
    </div>
  </van-form>
  <van-form @submit="onSubmit" v-if="editUser.editName!== '用户头像'&& editUser.editName!=='性别'">
    <van-field
        v-model="editUser.currentValue"
        :name="editUser.editKey"
        :label="editUser.editName"
        :placeholder="`请输入${editUser.editName}`"
    />
    <div style="margin: 16px;">
      <van-button round block type="primary" native-type="submit">
        修改
      </van-button>
    </div>
  </van-form>
</template>

<script setup lang="ts">
import {useRoute, useRouter} from "vue-router";
import {onMounted, ref} from "vue";
import myAxios from "../plugins/myAxios";
import {Toast} from "vant";
import {getCurrentUser} from "../services/user";

const route = useRoute();
const router = useRouter();
const fileList = ref([]);
const editUser = ref({
  editKey: route.query.editKey,
  currentValue: route.query.currentValue,
  editName: route.query.editName,
})


const beforeRead = (file: any) => {
  console.log(file+'jpg')
  if (file.type !== 'image/jpeg') {
    Toast('请上传 jpg 格式图片');
    return false;
  }
  return true;
};

const afterRead = (file: any) => {
  // 返回图片信息
  console.log(file);
  const ImgUploadFile = async (params: any) => {
    // 要把数据变成file格式
    const formData = new FormData(); // 下面有备注
    formData.append('file', params);
    console.log(formData)
    return await myAxios.post('/upload/img', formData, {
      headers: {
        // 注意修改请求头file格式
        'Content-Type': 'multipart/form-data',
      },
    });
  };
  ImgUploadFile(file.file)
  //牛客云上绑定的域名+文件名
  editUser.value.currentValue =  "xxx"+ file.file.name;
}
const checked = ref();
// 获取之前的用户头像信息
onMounted( () => {
      if (route.query.editName === "用户头像") {
        fileList.value = [{url: route.query.currentValue, isImage: true}]
      }
      if(route.query.editName === '性别'){
        if(route.query.currentValue ==="男"){
          checked.value = '男';
        }else {
          checked.value = '女';
        }
      }
    }
)
const checkedChange = ()=>{
  editUser.value.currentValue = checked.value
}
const onSubmit = async () => {
  const currentUser = await getCurrentUser();
  if (!currentUser) {
    Toast.fail('用户未登录');
    return;
  }
  console.log(currentUser, '当前用户')
  const res = await myAxios.post('/user/update', {
    'id': currentUser.id,
    [editUser.value.editKey as string]: editUser.value.currentValue,
  })
  console.log(res, '更新请求');
  if (res.code === 0 && res.data > 0) {
    Toast.success('修改成功');
    router.back();
  } else {
    Toast.fail('修改错误');
  }
};

</script>

<style scoped>

</style>
