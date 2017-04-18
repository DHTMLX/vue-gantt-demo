<template>
  <div ref="gantt"></div>
</template>

<script>
import 'dhtmlx-gantt'
export default {
  name: 'gantt',
  props: {
    ganttTasks: {
      type: Object,
      default: function () {
        return {data: [], links: []};
      }
    }
  },
 data: function () {
    return {
      tasks: this.ganttTasks
    }
  },

  mounted: function () {
    var _this = this

    gantt.attachEvent('onTaskSelected', function(id){
      var task = gantt.getTask(id)
      task = _this.serializeTask(task)
      _this.$emit('task-selected', task)
    });

    gantt.attachEvent('onAfterTaskAdd', function(id, task){
      task = _this.serializeTask(task)
      _this.$emit('task-updated', id, 'inserted', task)
      if(gantt.getSelectedId() == id) {
        _this.$emit('task-selected', task)
      }
    });

    gantt.attachEvent('onAfterTaskUpdate', function(id, task){
      task = _this.serializeTask(task)
      _this.$emit('task-updated', id, 'updated', task)
      if(gantt.getSelectedId() == id) {
        _this.$emit('task-selected', task)
      }
    });

    gantt.attachEvent('onAfterTaskDelete', function(id){
      _this.$emit('task-updated', id, 'deleted')
      if(!gantt.getSelectedId()) {
        _this.$emit('task-selected', null)
      }
    });

    gantt.attachEvent('onAfterLinkAdd', function(id, link){
      link = _this.serializeLink(link)
      _this.$emit('link-updated', id, 'inserted', link)
    });

    gantt.attachEvent('onAfterLinkUpdate', function(id, link){
      link = _this.serializeLink(link)
      _this.$emit('link-updated', id, 'updated', link)
    });

    gantt.attachEvent('onAfterLinkDelete', function(id, link){
      link = _this.serializeLink(link)
      _this.$emit('link-updated', id, 'deleted')
    });
  
    gantt.init(this.$refs.gantt)
    gantt.parse(this.tasks)
  },
  
  methods: {
    dateToStr: gantt.date.date_to_str("%F %j, %Y"),

    serializeTask: function(task){
      return {
        id: task.id,
        text: task.text,
        progress: task.progress,
        start_date: this.dateToStr(task.start_date),
        end_date: this.dateToStr(task.end_date)
      }
    },

    serializeLink: function(link){
      return {
        id: link.id,
        source: link.source,
        target: link.target,
        type: link.type
      }
    }
  }
}
</script>

<style>
	@import "~dhtmlx-gantt/codebase/dhtmlxgantt.css";
</style>