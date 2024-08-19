<template>
  <div class="main">
  <van-list
      v-model:loading="props.loading"
      :finished="props.finished"
      :immediate-check="false"
      finished-text="没有更多了"
      @load="handleLoad"
  >

    <van-skeleton title avatar :row="3" :loading="props.loading" v-for="user in props.userList">
      <van-card class="custom-desc-limit"
          :desc="user.profile"
          :title="`${user.username}（${user.email}）`"
          :thumb="user.avatarUrl"
      >
        <template #tags>
          <van-tag plain type="danger" v-for="tag in user.tags" style="margin-right: 8px; margin-top: 8px">
            {{ tag }}
          </van-tag>
        </template>
        <template #footer>
          <van-button size="mini">联系我</van-button>
        </template>
      </van-card>

    </van-skeleton>
  </van-list>
  </div>
</template>

<script setup lang="ts">
import {UserType} from "../models/user";


interface UserCardListProps {
  loading: boolean;
  userList: UserType[];
  finished: boolean,

}

const props = withDefaults(defineProps<UserCardListProps>(), {
  loading: false,
  // @ts-ignore
  userList: [] as UserType[],
  finished: false,
});
const emit = defineEmits([ "onload" ]);
const doSth = () => {
  emit('onload');
}
const handleLoad = () => {
  // console.log(props)
  doSth();
}

</script>

<style scoped>


</style>
