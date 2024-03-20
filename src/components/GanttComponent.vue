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

  mounted: function () {
    let gantt = Gantt.getGanttInstance();
    gantt.config.date_format = "%Y-%m-%d";

    gantt.init(this.$refs.ganttContainer);
    gantt.parse(this.$props.tasks);

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

    gantt.createDataProcessor((entity, action, data, id) => { 
      this.$emit(`${entity}-updated`, id, action, data);
    });
    
  },

  unmounted() {
    this.gantt.destructor();
  },
}
</script>

<style>
    @import "@dhx/trial-gantt/codebase/dhtmlxgantt.css";
</style>