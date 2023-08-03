<template>
  <a-card :title="title" style="width: 450px; height: 450px;">
    <div slot="extra">
      <slot name="extra"/>
    </div>
    <slot name="content"/>
  </a-card>
</template>
<script>
export default {
  props: {
    title: {
      type: String
    }
  }
}
</script>
