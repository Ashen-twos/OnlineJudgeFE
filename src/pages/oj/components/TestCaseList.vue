<template>
    <Card style="width:100%">
        <template #title>
          <Icon type="information-circled"></Icon>
            {{name}}
        </template>
        <Spin fix v-show="!isDone"></Spin>
        <!-- <p class="rate-demo" v-for="item in caseList">
            <a :href="item.url" target="_blank">{{ item.name }}</a>
            <span>
              <Icon type="checkmark" color="green" v-if="item.pass"></Icon>
              <Icon type="close" color="red" v-else></Icon>
            </span>
        </p> -->
        <Col>
          <Table stripe :columns="columns" :disabled-hover="true" :data="testCases"></Table>
        </Col>
    </Card> 
</template>

<script>
  export default {
    name: 'TestCaseVue',
    props: ['testCases', 'columns', 'name'],
    data () {
      return {
        caseList: [],
        isDone: true
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

</style>
