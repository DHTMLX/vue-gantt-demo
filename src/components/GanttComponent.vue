<template>
  <div ref="ganttContainer"></div>
</template>

<script>
import { Gantt } from "@dhx/trial-gantt";

export default {
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
      if (!this.gantt.$_eventsInitialized) {
        this.gantt.attachEvent('onTaskSelected', (id) => {
          let task = this.gantt.getTask(id);
          this.$emit('task-selected', task);
        });

        this.gantt.attachEvent('onTaskIdChange', (id, new_id) => {
          if (this.gantt.getSelectedId() == new_id) {
            let task = this.gantt.getTask(new_id);
            this.$emit('task-selected', task);
          }
        });

        this.gantt.$_eventsInitialized = true;
      }
    },

    $_initDataProcessor: function() {
      if (!this.gantt.$_dataProcessorInitialized) {
        this.gantt.createDataProcessor((entity, action, data, id) => { 
          this.$emit(`${entity}-updated`, id, action, data);
        });

        this.gantt.$_dataProcessorInitialized = true;
      }
    }
  },

  mounted: function () {
    let gantt = Gantt.getGanttInstance();
    this.gantt = gantt;
    this.$_initGanttEvents();
    gantt.config.date_format = "%Y-%m-%d";

    gantt.init(this.$refs.ganttContainer);
    gantt.parse(this.$props.tasks);

    this.$_initDataProcessor();
  },

  unmounted() {
    this.gantt.destructor();
  },
}
</script>

<style>
    @import "@dhx/trial-gantt/codebase/dhtmlxgantt.css";
</style>