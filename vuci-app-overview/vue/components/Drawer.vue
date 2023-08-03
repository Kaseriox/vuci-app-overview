<template>
  <div class="drawer">
    <a-button class="button" type="primary" @click="showDrawer">
      Open Card Visibility Toggle
    </a-button>
    <a-drawer
      :title="title"
      placement="right"
      :closable="true"
      :visible="visible"
      :after-visible-change="afterVisibleChange"
      @close="onClose"
      width="1024"
    >
    <slot name="content"/>
    </a-drawer>
  </div>
</template>
<script>
export default {
  props: {
    title: {
      type: String
    }
  },
  data () {
    return {
      visible: false
    }
  },
  methods: {
    afterVisibleChange (val) {
      console.log('visible', val)
      if (!val) {
        this.$emit('DrawerClosed')
      }
    },
    showDrawer () {
      this.visible = true
    },
    onClose () {
      this.visible = false
    }
  }
}
</script>
<style scoped>
.drawer{
    display: flex;
    justify-content: flex-end;
    margin-right: 5px;
}
</style>
