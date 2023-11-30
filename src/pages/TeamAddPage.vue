<template>
  <Top />
  <div id="teamAddPage">
    <van-form @submit="onSubmit">
      <van-cell-group inset>
        <van-uploader v-model="fileList" multiple :max-count="1" :before-read="beforeRead" :after-read="afterRead" />
        <van-field
            v-model="addTeamData.name"
            name="name"
            label="队伍名"
            placeholder="请输入队伍名"
            :rules="[{ required: true, message: '请输入队伍名' }]"
        />
        <van-field
            v-model="addTeamData.description"
            rows="4"
            autosize
            label="队伍描述"
            type="textarea"
            placeholder="请输入队伍描述"
        />
        <van-field
            is-link
            readonly
            name="datetimePicker"
            label="过期时间"
            :placeholder="addTeamData.expireTime ?? '点击选择过期时间'"
            @click="showPicker = true"
        />
        <van-popup v-model:show="showPicker" position="bottom">
          <van-datetime-picker
              v-model="addTeamData.expireTime"
              @confirm="showPicker = false"
              type="datetime"
              title="请选择过期时间"
              :min-date="minDate"
          />
        </van-popup>
        <van-field name="stepper" label="最大人数">
          <template #input>
            <van-stepper v-model="addTeamData.maxNum" max="10" min="3"/>
          </template>
        </van-field>
        <van-field name="radio" label="队伍状态">
          <template #input>
            <van-radio-group v-model="addTeamData.status" direction="horizontal">
              <van-radio name="0">公开</van-radio>
              <van-radio name="1">私有</van-radio>
              <van-radio name="2">加密</van-radio>
            </van-radio-group>
          </template>
        </van-field>
        <van-field
            v-if="Number(addTeamData.status) === 2"
            v-model="addTeamData.password"
            type="password"
            name="password"
            label="密码"
            placeholder="请输入队伍密码"
            :rules="[{ required: true, message: '请填写密码' }]"
        />
      </van-cell-group>
      <div style="margin: 16px;">
        <van-button round block type="primary" native-type="submit">
          提交
        </van-button>
      </div>
    </van-form>
  </div>
  <Bottom />
</template>

<script setup lang="ts">

import {useRouter} from "vue-router";
import {ref} from "vue";
import myAxios from "../plugins/myAxios";
import {Toast} from "vant";
import Top from "../layouts/Top.vue";
import Bottom from "../layouts/Bottom.vue";
// const fileList = "ikun.png"
const router = useRouter();
// 展示日期选择器
const showPicker = ref(false);

const initFormData = {
  "teamAvatarUrl":"",
  "name": "",
  "description": "",
  "expireTime": null,
  "maxNum": 3,
  "password": "",
  "status": 0,
}

const minDate = new Date();

const addTeamData = ref({...initFormData})
const fileList = ref([

  // Uploader 根据文件后缀来判断是否为图片文件
  // 如果图片 URL 中不包含类型信息，可以添加 isImage 标记来声明
]);
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
  addTeamData.value.teamAvatarUrl =  "http://s4rm2leff.hn-bkt.clouddn.com/"+ file.file.name;
}

// 提交
const onSubmit = async () => {
  const postData = {
    ...addTeamData.value,
    status: Number(addTeamData.value.status)
  }
  // todo 前端参数校验
  const res = await myAxios.post("/team/add", postData);
  if (res?.code === 0 && res.data){
    Toast.success('添加成功');
    router.push({
      path: '/team',
      replace: true,
    });
  } else {
    Toast.success('添加失败');
  }
}



</script>

<style scoped>
.preview-cover {
  position: absolute;
  bottom: 0;
  box-sizing: border-box;
  width: 100%;
  padding: 4px;
  color: #fff;
  font-size: 12px;
  text-align: center;
  background: rgba(0, 0, 0, 0.3);
}
</style>
