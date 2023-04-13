<template>
  <Collapse>
    <Panels v-if="extra_config.format.enable">
      代码格式({{extra_score.format}}分)
      <template #content>
        <Row>
          <Col :span="12"><p class="content">缩进长度:  <Tag color="green">{{extra_config.format.indent_size}}</Tag></p></Col>
          <Col :span="12"><p class="content">左大括号位置:  <Tag color="green">{{location}}</Tag></p></Col>
        </Row>
        <Row>
          <Col :span="12"><p class="content">单行语句上限:  <Tag color="green">{{extra_config.format.max_statement}}</Tag></p></Col>
          <Col :span="12"><p class="content">逗号和分号后空格:  
            <Tag color="green" v-if="extra_config.format.comma_space">{{switchs(extra_config.format.comma_space)}}</Tag>
            <Tag color="red" v-if="!extra_config.format.comma_space">{{switchs(extra_config.format.comma_space)}}</Tag></p></Col>
        </Row>
        <FormatPreview :indentSize="extra_config.format.indent_size"
                    :leftBigPara="extra_config.format.left_big_para"
                    :commaSpace="extra_config.format.comma_space" 
                    :maxStatement="extra_config.format.max_statement"></FormatPreview>
      </template>
    </Panels>

    <Panels v-if="extra_config.style.enable">
      代码风格({{extra_score.runtime}}分)
      <template #content>
        <Row>
          <Col :span="8"><p class="content">全局变量前缀:  <Tag color="green">{{extra_config.style.global_prefix}}</Tag></p></Col>
          <Col :span="8"><p class="content">禁用单字母命名:  <Tag color="green">{{switchs(extra_config.style.single_name)}}</Tag></p></Col>
        </Row>
        <Row>
          <Col :span="8"><p class="content">函数命名风格:  <Tag color="green">{{styles(extra_config.style.func_naming)}}</Tag></p></Col>
          <Col :span="8"><p class="content">全局变量命名风格:  <Tag color="green">{{styles(extra_config.style.global_naming)}}</Tag></p></Col>
          <Col :span="8"><p class="content">局部变量命名风格:  <Tag color="green">{{styles(extra_config.style.local_naming)}}</Tag></p></Col>
        </Row>
        <Row>
          <Col :span="8">
            <p class="content">下列命名除外: 
            <Tag color="green" v-for="para in extra_config.style.white_list" :key="para">{{ para }}</Tag></p>
          </Col>
        </Row>
      </template>
    </Panels>

    <Panels v-if="extra_config.function.enable">
      函数使用({{extra_score.function}}分)
      <template #content>
        <Row>
          <Col :span="24"><p class="content"> 
            不应使用的函数: 
            <Tag color="red" v-for="para in extra_config.function.black_list" :key="para">{{ para }}</Tag>
          </p></Col>
        </Row>
        <Row>
          <Col :span="24"><p class="content"> 
            必须使用的函数: 
            <Tag color="green" v-for="para in extra_config.function.white_list" :key="para">{{ para }}</Tag>
          </p></Col>
        </Row>
        <Row>
          <Col :span="12"><p class="content">函数内语句上限:  <Tag color="green">{{extra_config.function.max_statement}}</Tag></p></Col>
          <Col :span="12"><p class="content">函数内禁止IO:  <Tag color="green">{{switchs(extra_config.function.disableIO)}}</Tag></p></Col>
        </Row>
      </template>
    </Panels>

    <Panels v-if="extra_config.memory.enable">
      内存使用({{extra_score.memory}}分)
      <template #content>
        <Row>
          <Col :span="24"><p class="content"> 
            仅定义以下数组: 
            <Tag color="green" v-for="para in extra_config.memory.white_list" :key="para">{{ para }}</Tag>
          </p></Col>
        </Row>
        <Row>
          <Col :span="12"><p class="content">指针内存释放检查:  <Tag color="green">{{switchs(extra_config.memory.check_ptr_free)}}</Tag></p></Col>
        </Row>
      </template>
    </Panels>

    <Panels v-if="extra_config.runtime.enable">
      运行效率({{extra_score.runtime}}分)
      <template #content>
        <Row>
          <Col :span="12"><p class="content">运行时间:  <Tag color="green">{{extra_config.runtime.time}}</Tag></p></Col>
          <Col :span="12"><p class="content">内存占用:  <Tag color="green">{{extra_config.runtime.memory}}</Tag></p></Col>
        </Row>
      </template>
    </Panels>
  </Collapse>
</template>

<script>
import {Panel as Panels} from 'iview'
import FormatPreview from './FormatPreview.vue'
export default {
  name: 'formatpreview',
  components: {
    Panels,
    FormatPreview
  },
  data () {
    return {}
  },
  props: ['extra_config', 'extra_score'],
  computed: {
    location () {
      if (this.extra_config.format.left_big_para) {
        return '行首'
      } else {
        return '行末'
      }
    }
  },
  methods: {
    switchs (obj) {
      if (obj) {
        return '开启'
      } else {
        return '关闭'
      }
    },
    styles (obj) {
      if (obj === 0) {
        return '无限制'
      } else if (obj === 1) {
        return '复杂驼峰式'
      } else if (obj === 2) {
        return '全大写'
      } else if (obj === 3) {
        return '全小写'
      }
    }
  }
}
</script>

<style scoped lang="less">
.title {
  font-size: 20px;
  font-weight: 400;
  color: #3091f2;
}
.content {
  margin-left: 15px;
  margin-right: 20px;
  font-size: 16px
}
</style>
