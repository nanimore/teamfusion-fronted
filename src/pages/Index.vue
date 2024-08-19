<template>
  <van-cell center title="匹配模式">
    <template #right-icon>
      <van-switch v-model="isMatchMode" size="24" @change="handleSwitchChange"/>
    </template>
  </van-cell>
  <user-card-list :userList="userList" :loading="loading" :finished="finished" @onload="loadData"/>


  <van-empty v-if="!userList || userList.length < 1" description="数据为空"/>
</template>

<script setup lang="ts">
import {ref, watchEffect} from 'vue';
import myAxios from "../plugins/myAxios";
import {Toast} from "vant";
import UserCardList from "../components/UserCardList.vue";
import {UserType} from "../models/user";

const isMatchMode = ref<boolean>(false);

const userList = ref([]);
const loading = ref<boolean>(true);
const finished = ref<boolean>(false);

const pageNum = ref(1);
const pageNum2 = ref(1);
const pageSize = ref(10);

let userListData: UserType[] = [];
let userListData2: UserType[] = [];

/**
 * 加载数据
 */
const loadData = async () => {

  let copiedList = JSON.parse(JSON.stringify(userListData));

  loading.value = true;
  // 匹配模式，根据标签匹配用户
  if (isMatchMode.value) {
    const num = 10;
    userListData2 = [];

    userListData2 = await myAxios.get('/user/match', {
      params: {
        num: pageSize.value * pageNum2.value++,
      },
    })
        .then(function (response) {
          console.log('/user/match succeed', response);
          return response?.data;
        })
        .catch(function (error) {
          console.error('/user/match error', error);
          Toast.fail('请求失败');
        })

    if (userListData2) {
      userListData2.forEach((user: UserType) => {
        if (user.tags) {
          user.tags = JSON.parse(user.tags);
        }
      })
    }

    userList.value = userListData2;

  } else {
    // 普通模式，直接分页查询用户
    userListData = await myAxios.get('/user/recommend', {
      params: {
        pageSize: pageSize.value,
        pageNum: pageNum.value++,
      },
    })
        .then(function (response) {
          console.log('/user/recommend succeed', response);
          return response?.data?.records;
        })
        .catch(function (error) {
          console.error('/user/recommend error', error);
          Toast.fail('请求失败');
        })
    if (userListData) {
      userListData.forEach((user: UserType) => {
        if (user.tags) {
          user.tags = JSON.parse(user.tags);
        }
      })
    }

    userList.value = [...copiedList, ...userListData]
    userListData = [...copiedList, ...userListData]
  }

  if (userList.value.length > 500) {
    finished.value = false;
  }


  loading.value = false;
}


loadData();


// watchEffect(() => {
//   loadData();
// })

// 定义一个处理开关状态改变的方法
const handleSwitchChange = (value) => {
  console.log('Switch changed to:', value);
  loadData();
  // 这里可以添加更多的逻辑，比如根据开关的状态发送请求或更改其他数据
};
</script>

<style scoped>

</style>
