<template>
  <div
    class="c-graph-node"
    :id="node.id"
  >
    <div
      class="container"
    >
      <task
        :status="node.state"
        :isHeld="node.isHeld"
      ></task>
      <div>
        <span class="name">{{ node.task.name }}</span>
        <br />
        <span class="cycle-point">{{ node.cyclePoint }}</span>
        <br />
        <jobbox
          :jobs="node.jobs"
          :maxItems="5"
        ></jobbox>
      </div>
    </div>
  </div>
</template>

<script>
import Task from '@/components/cylc/Task'
import JobBox from '@/components/cylc/JobBox'

export default {
  name: 'GraphNode',
  components: {
    task: Task,
    jobbox: JobBox
  },
  props: {
    node: {
      type: Object,
      required: true
    }
  }
}
</script>

<style lang="scss">
.c-graph-node {
  display: inline-block;

  div.container {
    display: flex;
    align-items: center;

    .c-task {
      font-size: 4em;
      margin-right: 0.3em;
    }

    span.name, span.cycle-point {
      font-size: 1.2em;
    }

  }
}
</style>
