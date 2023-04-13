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
    maxStatement: {
      type: Number
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
      let code = 'int func(int a,int b,int c)\n{\n' + ' '.repeat(this.indentSize) + 'a++;b++;'
      if (this.maxStatement < 3) {
        code += '\n' + ' '.repeat(this.indentSize)
      }
      code += 'c--;\n' + ' '.repeat(this.indentSize) + 'return a+b+c;\n}'
      if (!this.leftBigPara) {
        code = code.replace('\n{', '{')
      }
      if (this.commaSpace) {
        let regc = new RegExp(',', 'g')
        let regf = new RegExp(';', 'g')
        code = code.replace(regc, ', ')
        code = code.replace(regf, '; ')
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
