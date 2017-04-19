<template>
  <div ref="gantt"></div>
</template>

<script>
import 'dhtmlx-gantt';

export default {
  name: 'gantt',
  props: {
    tasks: {
      type: Object,
      default: function () {
        return {data: [], links: []};
      }
    }
  },

  mounted: function () {

    gantt.attachEvent('onTaskSelected', (id) => {
      var task = gantt.getTask(id)
      this.$emit('task-selected', task)
    });

    gantt.attachEvent('onAfterTaskAdd', (id, task) => {
      this.$emit('task-updated', id, 'inserted', task)
    });

    gantt.attachEvent('onAfterTaskUpdate', (id, task) => {
      this.$emit('task-updated', id, 'updated', task)
    });

    gantt.attachEvent('onAfterTaskDelete', (id) => {
      this.$emit('task-updated', id, 'deleted')
      if(!gantt.getSelectedId()) {
        this.$emit('task-selected', null)
      }
    });

    gantt.attachEvent('onAfterLinkAdd', (id, link) => {
      this.$emit('link-updated', id, 'inserted', link)
    });

    gantt.attachEvent('onAfterLinkUpdate', (id, link) => {
      this.$emit('link-updated', id, 'updated', link)
    });

    gantt.attachEvent('onAfterLinkDelete', (id, link) => {
      this.$emit('link-updated', id, 'deleted')
    });
  
    gantt.init(this.$refs.gantt)
    gantt.parse(this.$props.tasks)
  }
}
</script>

<style>
	@import "~dhtmlx-gantt/codebase/dhtmlxgantt.css";
</style>