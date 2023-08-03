<template>
    <a-list :data-source="data">
        <a-list-item slot="renderItem" slot-scope="item">
            <a-list-item-meta >
                <h1 slot="title">{{ item.title }}</h1>
                <div slot="description" >
                    <slot :name="item.title">
                        {{ item.content }}
                    </slot>
                </div>
            </a-list-item-meta>
        </a-list-item>
    </a-list>
</template>
<script>
export default {
  props: {
    data: {
      type: Array,
      required: true
    }
  }
}
</script>
