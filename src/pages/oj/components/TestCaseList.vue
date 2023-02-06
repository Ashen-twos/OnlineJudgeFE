<template>
    <Card style="width:100%">
        <template #title>
          <Icon type="information-circled"></Icon>
            测试点详情
        </template>
        <Spin fix v-show="!isDone"></Spin>
        <p class="rate-demo" v-for="item in caseList">
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
    props: ['testCases'],
    data () {
      return {
        caseList: [],
        isDone: false
      }
    },
    mounted () {
      console.log(this.caseList)
    },
    watch: {
      testCases (curVal, oldVal) {
        if (curVal) {
          console.log('List Get:', curVal)
          this.caseList = curVal
          this.isDone = true
        }
      }
    }
  }
</script>

<style>
.rate-demo {
    display: flex;
    justify-content: space-between;
    align-items: center;   
    line-height: 40px;
}

</style>
