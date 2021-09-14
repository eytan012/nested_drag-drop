<template>
  <div class="dd-handle">
    <span class="label label-info"></span>
    {{ item.name }}
    <div v-if="item.tasks.length > 0">
      <div v-for="child in item" :key="child.name">
        <div v-if="child">
                <children-of-item v-for="baby of child" :baby="baby" :key="baby.name"/>
        </div>
      </div>
    </div>
  </div>
</template>

<script>

import draggable from 'vuedraggable';
import childrenOfItem from './childrenOfItem.vue';
export default {
  components: { draggable,childrenOfItem },
  props: {
    item: Object,
  },
  mounted: function(){
      console.log(this.$props);
  }
};
</script>

<style>
</style>