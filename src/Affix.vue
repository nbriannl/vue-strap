<template>
  <div class="hidden-print hidden-xs hidden-sm">
    <nav class="bs-docs-sidebar" :class="{affix:affixed}" :style="{marginTop:top}">
      <slot></slot>
    </nav>
  </div>
</template>

<script>
import {toNumber} from './utils/utils.js'
import $ from './utils/NodeList.js'

export default {
  props: {
    offset: {
      type: Number,
      default: 0
    }
  },
  data () {
    return {
      affixed:false
    }
  },
  computed: {
    offsetNumber() {
      return toNumber(this.offset)
    },
    top () {
      return this.offsetNumber > 0 ? this.offsetNumber + 'px' : null
    }
  },
  methods: {
    // from https://github.com/ant-design/ant-design/blob/master/components/affix/index.jsx#L20
    checkScroll () {
      // if is hidden don't calculate anything
      if (!(this.$el.offsetWidth || this.$el.offsetHeight || this.$el.getClientRects().length)) { return }
      // get window scroll and element position to detect if have to be normal or affixed
      let scroll = {}
      let element = {}
      const rect = this.$el.getBoundingClientRect()
      const body = document.body;
      ['Top','Left'].forEach(type => {
        let t = type.toLowerCase()
        let ret = window['page' + (type==='Top' ? 'Y' : 'X') + 'Offset']
        const method = 'scroll' + type
        if (typeof ret !== 'number') {
          // ie6,7,8 standard mode
          ret = document.documentElement[method]
          if (typeof ret !== 'number') {
            // quirks mode
            ret = document.body[method]
          }
        }
        scroll[t] = ret
        element[t] = scroll[t] + rect[t] - (this.$el['client'+type] || body['client'+type] || 0)
      })
      let fix = scroll.top > element.top - this.offsetNumber
      if (this.affixed !== fix) { this.affixed = fix }
    }
  },
  mounted () {
    $(window).on('scroll resize', () => this.checkScroll())
    setTimeout(() => this.checkScroll(), 0)
  },
  beforeDestroy () {
    $(window).off('scroll resize', () => this.checkScroll())
  }
}
</script>
