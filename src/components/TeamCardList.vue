<template>
<!--  <div-->
<!--      id="teamCardList"-->
<!--  >-->
<!--    <van-card-->
<!--        v-for="team in props.teamList"-->
<!--        :thumb="team.teamAvatarUrl"-->
<!--        :desc="team.description"-->
<!--        :title="`${team.name}`"-->
<!--    >-->
<!--      <template #tags>-->
<!--        <van-tag plain type="danger" style="margin-right: 8px; margin-top: 8px">-->
<!--          {{-->
<!--            teamStatusEnum[team.status]-->
<!--          }}-->
<!--        </van-tag>-->
<!--      </template>-->
<!--      <template #bottom>-->
<!--        <div>-->
<!--          {{ `队伍人数: ${team.hasJoinNum}/${team.maxNum}` }}-->
<!--        </div>-->
<!--        <div v-if="team.expireTime">-->
<!--          {{ '过期时间: ' + team.expireTime }}-->
<!--        </div>-->
<!--        <div>-->
<!--          {{ '创建时间: ' + team.createTime }}-->
<!--        </div>-->
<!--      </template>-->
<!--      <template #footer>-->
<!--        <van-button size="small" type="primary" v-if="team.userId !== currentUser?.id && !team.hasJoin" plain-->
<!--                    @click="preJoinTeam(team)">-->
<!--          加入队伍-->
<!--        </van-button>-->
<!--        <van-button v-if="team.userId === currentUser?.id" size="small" plain-->
<!--                    @click="doUpdateTeam(team.id)">更新队伍-->
<!--        </van-button>-->
<!--        &lt;!&ndash; 仅加入队伍可见 &ndash;&gt;-->
<!--        <van-button v-if="team.userId !== currentUser?.id && team.hasJoin" size="small" plain-->
<!--                    @click="doQuitTeam(team.id)">退出队伍-->
<!--        </van-button>-->
<!--        <van-button v-if="team.userId === currentUser?.id" size="small" type="danger" plain-->
<!--                    @click="doDeleteTeam(team.id)">解散队伍-->
<!--        </van-button>-->
<!--      </template>-->
<!--    </van-card>-->
<!--    <van-dialog v-model:show="showPasswordDialog" title="请输入密码" show-cancel-button @confirm="doJoinTeam" @cancel="doJoinCancel">-->
<!--      <van-field v-model="password" placeholder="请输入密码"/>-->
<!--    </van-dialog>-->
<!--  </div>-->
  <div v-for="(team, index) in props.teamList" :key="index" id="teamCardList" class="team-card">
    <img  :src="team?.teamAvatarUrl ?? defaultPic" alt="Team Avatar" class="team-avatar" />
    <div class="team-info">
      <h3 class="team-name">{{ team.name }}</h3>
      <p class="team-description"><span class="tip">队伍描述：</span>{{ team.description }}</p>
      <p class="team-members">
        <span class="tip">
          最大人数：
        </span>
        {{ team.hasJoinNum }} / {{ team.maxNum }}
      </p>

      <div class="status">

        <div class="team-status" v-if="team.status === 0">
          <span class="tip">房间状态：</span><van-tag type="primary">公开</van-tag>
        </div>
        <div class="team-status" v-else-if="team.status === 1">
          <span class="tip">房间状态：</span><van-tag type="warning">私有</van-tag>
        </div>
        <div class="team-status" v-else-if="team.status === 2">
          <span class="tip">房间状态：</span><van-tag type="success">加密</van-tag>
        </div>
      </div>
<!--      <div class="avas">-->
<!--        <a-avatar-group>-->
<!--          <a-avatar :src="team.createUser?.avatarUrl" />-->
<!--          <a-avatar style="background-color: #f56a00">K</a-avatar>-->
<!--          <a-tooltip title="队伍成员" placement="top">-->
<!--            <a-avatar style="background-color: #87d068">-->
<!--              <template #icon>-->
<!--                <UserOutlined />-->
<!--              </template>-->
<!--            </a-avatar>-->
<!--          </a-tooltip>-->
<!--          <a-avatar style="background-color: #1890ff">-->
<!--            <template #icon>-->
<!--              <AntDesignOutlined />-->
<!--            </template>-->
<!--          </a-avatar>-->
<!--        </a-avatar-group>-->
<!--      </div>-->
    </div>
    <div class="showTeamStatus">
      <van-button size="small" type="primary" v-if="team.userId !== currentUser?.id && !team.hasJoin" plain @click="preJoinTeam(team)"> 加入队伍
      </van-button>
      <van-button v-if="team.userId === currentUser?.id" size="small" plain @click="doUpdateTeam(team.id)">更新队伍
      </van-button>
      <!-- 仅加入队伍可见 -->
      <van-button v-if="team.userId !== currentUser?.id && team.hasJoin" size="small" plain @click="doQuitTeam(team.id)">退出队伍
      </van-button>
      <van-button v-if="team.userId === currentUser?.id" size="small" type="danger" plain @click="doDeleteTeam(team.id)">解散队伍
      </van-button>
    </div>
    <div>
      <van-dialog v-model:show="showPasswordDialog" title="请输入密码" show-cancel-button @confirm="doJoinTeam" @cancel="doJoinCancel">
        <van-field v-model="password" placeholder="请输入密码"/>
      </van-dialog>
    </div>
  </div>
</template>

<script setup lang="ts">
import {TeamType} from "../models/team";
import {teamStatusEnum} from "../constants/team";
import myAxios from "../plugins/myAxios";
import {Dialog, Toast} from "vant";
import {onMounted, ref} from "vue";
import {getCurrentUser} from "../services/user";
import {useRouter} from "vue-router";
const defaultPic = "https://fastly.jsdelivr.net/npm/@vant/assets/ipad.jpeg"
interface TeamCardListProps {
  teamList: TeamType[];
}

const props = withDefaults(defineProps<TeamCardListProps>(), {
  // @ts-ignore
  teamList: [] as TeamType[],
});

const showPasswordDialog = ref(false);
const password = ref('');
const joinTeamId = ref(0);
const currentUser = ref();

const router = useRouter();

onMounted(async () => {
  currentUser.value = await getCurrentUser();
})

const preJoinTeam = (team: TeamType) => {
  joinTeamId.value = team.id;
  if (team.status === 0) {
    doJoinTeam()
  } else {
    showPasswordDialog.value = true;
  }
}

const doJoinCancel = () => {
  joinTeamId.value = 0;
  password.value = '';
}

/**
 * 加入队伍
 */
const doJoinTeam = async () => {
  if (!joinTeamId.value) {
    return;
  }
  const res = await myAxios.post('/team/join', {
    teamId: joinTeamId.value,
    password: password.value
  });
  if (res?.code === 0) {
    Toast.success('加入成功');
    doJoinCancel();
  } else {
    Toast.fail('加入失败' + (res.description ? `，${res.description}` : ''));
  }
}

/**
 * 跳转至更新队伍页
 * @param id
 */
const doUpdateTeam = (id: number) => {
  router.push({
    path: '/team/update',
    query: {
      id,
    }
  })
}

/**
 * 退出队伍
 * @param id
 */
const doQuitTeam = (id: number) => {
  Dialog.confirm({
    message:
        '是否退出队伍',
  })
      .then(async () => {
        // on confirm
        const res = await myAxios.post('/team/quit', {
          teamId: id
        });
        if (res?.code === 0) {
          Toast.success('操作成功');
        } else {
          Toast.fail('操作失败' + (res.description ? `，${res.description}` : ''));
        }
      })
      .catch(() => {
        // on cancel
      });

}

/**
 * 解散队伍
 * @param id
 */
const doDeleteTeam =  (id: number) => {
  Dialog.confirm({
  message:
      '确认解散队伍？？？',
  })
    .then(async () => {
      // on confirm
      const res = await myAxios.post('/team/delete', {
        id,
      });
      if (res?.code === 0) {
        Toast.success('操作成功');
      } else {
        Toast.fail('操作失败' + (res.description ? `，${res.description}` : ''));
      }
    })
    .catch(() => {
      // on cancel
    });

}

</script>

<style scoped>


.team-card {
  margin: 5px;
  display: flex;
  align-items: center;
  /* background-color: #f0f0f0; */
  border-radius: 10px;
  overflow: hidden;
  box-shadow: 0 2px 4px rgba(1, 2, 1, 0.2);
  padding: 10px;
}

.team-avatar {
  width: 90px;
  height: 90px;
  object-fit: cover;
  /* border-radius: 50%; */
  margin: 10px;
  border-radius: 10px;

}

.team-info {
  flex: 1;
  padding: 2px;
//align-items: center;
  display: flex;
  justify-content: space-around;
  flex-direction: column;
//text-align: center;
}

.team-name {
  font-size: 16px;
  font-weight: 700;
  margin: 0;
}

.team-description {
  font-size: 13px;
  margin: 5px 0;
}

.team-members {
  font-size: 12px;
  color: #888;
  margin: 0;
}

.team-cover {
  width: 120px;
  height: 80px;
  object-fit: cover;
}

.showTeamStatus {
  position: relative;
  bottom: -40px;
  right: 0;
//margin-right: 8px;

}
.avas{
  padding: 2px;
}

.join_btn {
//margin: 3px 0 5px 0;
//color: #f8f3d4;
//font-size: 16px;
//height: 40px;
////line-height: 40px;
////text-align: center;
//background-color: #00b8a9;
//border-radius: 10%;

}

:deep(.van-overlay) {
  background: rgba(0, 0, 0, 0.1);
}
.tip{
  color: #999;
  font-size: 12px;
}
</style>
