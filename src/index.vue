<template>
  <div class="component">
    <ar-drawer
      :append-to-body='!inEditor'
      :wrapperClosable='!inEditor'
      :modal='!inEditor'
      :with-header='false'
      :visible.sync="visible"
      :direction="direction"
      :before-close="handleClose">
      <slot></slot>
    </ar-drawer>
  </div>
</template>

<script>
  import {VueExtend} from 'godspen-lib'
  import ArDrawer from './drawer'
  import './drawer.css'

  export default {
    mixins: [VueExtend.mixin],
    name: 'drawer-can',
    components: { ArDrawer },
    label: process.env.LABEL,
    style: process.env.STYLE,
    slots: { name: 'default' },
    props: {
      direction: {
        type: String,
        editor: {
          label: '推出方向',
          type: 'enum',
          defaultList: {
            ltr: '从左往右开',
            rtl: '从右往左开',
            ttb: '从上往下开',
            btt: '从下往上开',
          }
        },
        default: 'ltr'
      },
      closefn: {
        type: Array,
        default () {
          return []
        },
        editer: {
          label: '关闭回调',
          type: 'function'
        }
      }
    },
    data () {
      return {
        visible: false,
        inEditor: /edit[oe]r|preview/i.test(window.EDIT_TYPE)
      }
    },
    editorMethods: {
      open: {
        label: '打开抽屉',
        params: []
      },
      close: {
        label: '关闭抽屉',
        params: []
      }
    },
    mounted () {
      this.$watch(process.env.NODE_ENV !== 'production' ? '$parent.$parent.visible' : '$parent.visible', this.watchVisible, {immediate: true})
    },
    methods: {
      handleClose () {
        this.oncallExecute(this.closefn)
        this.close()
      },
      watchVisible (val, old) {
        this.visible = val
      },
      open () {
        var $com = this.getParent()
        $com.visible = true
      },
      getParent () {
        return process.env.NODE_ENV !== 'production' ? this.$parent.$parent : this.$parent
      },
      close () {
        var $com = this.getParent()
        $com.visible = false
      }
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus" type="text/stylus" scoped>
  .component {
    width: 0px;
    height: 0px;
  }
  body > .el-drawer__wrapper >>> .el-drawer__body {
    overflow auto
  }
</style>
