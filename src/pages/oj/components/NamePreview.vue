<template>
  <Highlight :code="realCode" language="C++"></Highlight>
</template>
  
<script>
import Highlight from '@/pages/oj/components/Highlight'
export default {
  name: 'namepreview',
  components: {
    Highlight
  },
  data () {
    return {
      value: '1'
    }
  },
  props: {
    globalNaming: {
      type: Number,
      default: 1
    },
    localNaming: {
      type: Number,
      default: 1
    },
    funcNaming: {
      type: Number,
      default: 1
    },
    globalPrefix: {
      type: String,
      default: ''
    }
  },
  computed: {
    globalName () {
      if (this.globalNaming <= 1) {
        return this.globalPrefix + 'HelloMyWorld'
      } else if (this.globalNaming === 2) {
        return this.globalPrefix + 'HELLOMYWORLD'
      } else if (this.globalNaming === 3) {
        return this.globalPrefix + 'hellomyworld'
      }
    },
    funcName () {
      if (this.funcNaming <= 1) {
        return 'MyFunction'
      } else if (this.funcNaming === 2) {
        return 'MYFUNCTION'
      } else if (this.funcNaming === 3) {
        return 'myfunction'
      }
    },
    localName () {
      if (this.localNaming <= 1) {
        return 'MySum'
      } else if (this.localNaming === 2) {
        return 'MYSUM'
      } else if (this.localNaming === 3) {
        return 'mysum'
      }
    },
    realCode () {
      let code = 'int ' + this.globalName + ';\n\n'
      code += 'void ' + this.funcName + '()\n{\n'
      code += '    int ' + this.localName + ';\n}\n'
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
