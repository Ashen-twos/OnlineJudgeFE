<template>
  <Collapse>
    <Panels v-if="extra_config.format.enable">
      代码格式({{extra_score.format}}分)
      <template #content>
        <Row>
          <Col :span="12"><p class="content">缩进长度: <Tag color="green">{{extra_config.format.indent_size}}</Tag></p></Col>
          <Col :span="12"><p class="content">左大括号位置: <Tag color="green">{{location}}</Tag></p></Col>
        </Row>
        <FormatPreview :indentSize = extra_config.format.indent_size
                    :leftBigPara = extra_config.format.left_big_para></FormatPreview>
      </template>
    </Panels>
    <Panels v-if="extra_config.function.enable">
      函数使用({{extra_score.function}}分)
      <template #content>
        不使用以下函数:<br>
        <Tag color="green" v-for="para in extra_config.function.function_list" :key="para">{{ para }}</Tag>
      </template>
    </Panels>
    <Panels v-if="extra_config.memory.enable">
      内存使用({{extra_score.memory}}分)
      <template #content>
        仅使用以下数组:<br>
        <Tag color="green" v-for="para in extra_config.memory.parameter" :key="para">{{ para }}</Tag>
      </template>
    </Panels>
    <Panels v-if="extra_config.runtime.enable">
      算法性能({{extra_score.runtime}}分)
      <template #content>
        程序运行时间在 <Tag color="green">{{extra_config.runtime.limit}}ms</Tag>以内
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

  },
  props: ['extra_config', 'extra_score'],
  computed: {
    location () {
      if (this.extra_config.format.left_big_para) {
        return '行末'
      } else {
        return '行首'
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
