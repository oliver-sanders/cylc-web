<template>
  <div class="c-graph">
    <h1>Graph 2</h1>
    <p>{{res}}</p>
    <div
      v-for="workflow in workflows"
    >
      <div
        class="node"
        :id="node.id"
        v-for="node in workflow.nodesEdges.nodes"
      >
        {{ node.task.name }}
        <br/>
        {{ node.cyclePoint }}
      </div>
    </div>
  </div>
</template>

<script>
import { workflowService } from 'workflow-service'
import { mixin } from '@/mixins/index'
import { mapState } from 'vuex'
import dagre from 'dagre'

const QUERIES = {
  root: `
        {
          workflows(ids: ["generic"]) {
            id
            status
            nodesEdges {
              nodes {
                id
                label: id
                parent: firstParent {
                  id
                  state
                }
                state
                cyclePoint
                task {
                  name
                }
                jobs(sort: {keys: ["submit_num"], reverse: true}) {
                  id
                  batchSysName
                  batchSysJobId
                  host
                  startedTime
                  submittedTime
                  finishedTime
                  state
                  submitNum
                }
              }
              edges {
                id
                source
                target
                label: id
              }
            }
          }
        }
    `
}

export default {
  name: 'graph',

  props: {
    workflowName: {
      type: String,
      required: true
    }
  },

  data: () => ({
    graph: null,
    res: 'waiting ...',
    viewID: '',
    workflowId: '',
    subscriptions: {},
    isLoading: true
  }),

  beforeDestroy () {
    if (tippy) {
      tippy.hide()
    }
    workflowService.unregister(this)
  },  

  created (cy) {
    this.workflowId = this.workflowName
    this.viewID = `Tree(${this.workflowId}): ${Math.random()}`
    workflowService.register(
      this,
      {
        activeCallback: this.setActive
      }
    )
    this.subscribe('root')

    this.reset()

    setInterval(() => {
      this.updateGraph()
    }, 5000)
    
  },

  computed: {
    ...mapState('workflows', ['workflows']),
  },

  methods: {
    subscribe (queryName) {
      const id = workflowService.subscribe(
        this,
        QUERIES[queryName].replace('WORKFLOW_ID', this.workflowId)
      )
      if (!(queryName in this.subscriptions)) {
        this.subscriptions[queryName] = {
          id
        }
      }
    },

    unsubscribe (queryName) {
      if (queryName in this.subscriptions) {
        workflowService.unsubscribe(
          this.subscriptions[queryName].id
        )
      }
    },

    reset () {
      this.graph = new dagre.graphlib.Graph()
      this.graph.setGraph({})
      this.graph.setDefaultEdgeLabel(function () { return {} })
    },

    updateGraph () {
      this.res = 'update'
      const graph = this.graph

      this.res = 'populate'
      this.workflows.forEach((workflow) => {
      })
      this.res = 'populate2'
      this.workflows.forEach((workflow) => {
        if ( ! workflow.nodesEdges ) {
          return
        }

        workflow.nodesEdges.nodes.forEach((node) => {
          graph.setNode(
            node.id,
            {
              id: node.id,
              label: node.task.name,
              width: 100,
              height: 100
            }
          )
        })

        workflow.nodesEdges.edges.forEach((edge) => {
          graph.setEdge(
            edge.source,
            edge.target
          )
        })
      })

      this.res = 'graphing'
      dagre.layout(graph)

      this.res = 'computing nodes'
      var ele
      var graphNode
      graph.nodes().forEach(function (node) {
        graphNode = graph.node(node)
        console.log('Node:', graphNode)
        ele = document.getElementById(graphNode.id)
        ele.style.top = `${graphNode.x}px`
        ele.style.left = `${graphNode.y}px`
      })
      this.res = 'computing edges'
      graph.edges().forEach(function (v) {
        console.log('Edge:', graph.edge(v))
      })

      console.log('done')
      this.res = 'done'
    }

  }

}
</script>

<style lang="scss">
.c-graph {
  .node {
    position: absolute;
    border: 1px solid black;
    top: 0px;
    left: 0px;
  }
}
</style>
