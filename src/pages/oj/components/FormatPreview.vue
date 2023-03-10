<template>
  <Highlight :code="realCode" language="C++"></Highlight>
</template>

<script>
import Highlight from '@/pages/oj/components/Highlight'
export default {
  name: 'formatpreview',
  components: {
    Highlight
  },
  data () {
    return {
      value: '1'
    }
  },
  props: {
    indentSize: {
      type: Number,
      default: 4
    },
    code: {
      type: String
    },
    leftBigPara: {
      type: Boolean,
      default: false
    },
    commaSpace: {
      type: Boolean,
      default: true
    }
  },
  computed: {
    realCode () {
      let code = 'int add(int a,int b)\n{\n' + ' '.repeat(this.indentSize) + 'return a+b;\n}'
      if (this.leftBigPara) {
        code = code.replace('\n{', '{')
      }
      if (this.commaSpace) {
        code = code.replace(',', ', ')
      }
      return code
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
