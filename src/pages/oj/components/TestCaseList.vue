<template>
    <Card style="width:100%">
        <template #title>
          <Icon type="information-circled"></Icon>
            测试点详情
        </template>
        <template #extra>
            <a href="#">
                <Icon type="ios-loop-strong"></Icon>
                刷新
            </a>
        </template>
        <p class="rate-demo" v-for="item in movieList">
            <a :href="item.url" target="_blank">{{ item.name }}</a>
            <span>
              <Icon type="checkmark" color="green" v-if="item.pass"></Icon>
              <Icon type="close" color="red" v-else></Icon>
            </span>
        </p>
    </Card>
</template>
<script>
  export default {
    name: 'TestCaseVue',
    props: ['movieList']
  }
</script>

<style>
.rate-demo {
    display: flex;
    justify-content: space-between;
    align-items: center;   
}
</style>
