<template>
  <draggable
    :emptyInsertThreshold="20"
    class="dragArea"
    @start="dragStart"
    @end="dragEnd"
    tag="ul"
    :list="tasks"
    :group="{ name: 'g1' }"
  >
    <li v-for="el in tasks" :key="el.id" class="dd-item">
      <div class="dd-handle">{{ el.name }}</div>
      <nested-draggable :tasks="el.tasks" />
    </li>
  </draggable>
</template>
<script>
import draggable from "vuedraggable";
export default {
  props: {
    tasks: {
      required: true,
      type: Array,
    },
  },
  components: {
    draggable,
  },
  methods: {
    dragStart(e) {},
    dragEnd(e) {
      const id = e;
      console.log(id);
      // const el = document.getElementById(id);
      // el.style.display = "block";
    },
  },
  name: "nested-draggable",
};
</script>
<style scoped>
.dragArea {
  min-height: 50px;
  outline: 1px dashed;
}
</style>