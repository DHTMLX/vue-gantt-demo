<template>
  <div ref="gantt"></div>
</template>

<script>
import 'dhtmlx-gantt'
export default {
  name: 'gantt',
  props: {
    tasks: {
      type: Object,
      default () {
        return {data: [], links: []}
      }
    }
  },

  methods: {
    $_initGanttEvents: function() {
      if (!gantt.$_eventsInitialized) {
        gantt.attachEvent('onTaskSelected', (id) => {
          let task = gantt.getTask(id);
          this.$emit('task-selected', task);
        });

        gantt.attachEvent('onTaskIdChange', (id, new_id) => {
          if (gantt.getSelectedId() == new_id) {
            let task = gantt.getTask(new_id);
            this.$emit('task-selected', task);
          }
        });

        gantt.$_eventsInitialized = true;
      }
    },

    $_initDataProcessor: function() {
      if (!gantt.$_dataProcessorInitialized) {
        gantt.createDataProcessor((entity, action, data, id) => { 
          this.$emit(`${entity}-updated`, id, action, data);
        });

        gantt.$_dataProcessorInitialized = true;
      }
    }
  },

  mounted: function () {
    this.$_initGanttEvents();
    gantt.config.xml_date = "%Y-%m-%d";

    gantt.init(this.$refs.gantt);
    gantt.parse(this.$props.tasks);

    this.$_initDataProcessor();
  }
}
</script>

<style>
    @import "~dhtmlx-gantt/codebase/dhtmlxgantt.css";
</style>