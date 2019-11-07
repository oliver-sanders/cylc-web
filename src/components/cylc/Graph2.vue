<template>
  <div class="c-graph">
    <div
      v-for="workflow in workflows"
    >
      <graphnode
        :node="node"
        v-for="node in workflow.nodesEdges.nodes"
      ></graphnode>
    </div>
  </div>
</template>

<script>
import { workflowService } from 'workflow-service'
import { mixin } from '@/mixins/index'
import { mapState } from 'vuex'
import dagre from 'dagre'
import GraphNode from '@/components/cylc/GraphNode'

const QUERIES = {
  root: `
        {
          workflows(ids: ["WORKFLOW_ID"]) {
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
                isHeld
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

  components: {
    graphnode: GraphNode
  },

  props: {
  },

  data: () => ({
    // view stuff
    viewID: '',
    workflowId: '',
    subscriptions: {},
    isLoading: true,

    // graph stuff
    graph: null,
    nodeDimensions: {}
  }),

  beforeDestroy () {
    if (tippy) {
      tippy.hide()
    }
    workflowService.unregister(this)
  },  

  created (cy) {
    this.workflowId = this.$route.params.workflowid
    this.viewID = `Tree(${this.workflowId}): ${Math.random()}`
    workflowService.register(
      this,
      {
        activeCallback: this.setActive
      }
    )
    this.subscribe('root')

    this.reset()

    // temporary workaround for a quick proof of concept, re-draw the entire
    // graph every five seconds
    this.updateGraph()
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

    recomputeDimensions () {
      for (let ele of document.getElementsByClassName('c-graph-node')) {
        this.nodeDimensions[ele.id] = {
          width: ele.clientWidth,
          height: ele.clientHeight
        }
      }
    },

    updateGraph () {
      // the graph can be built more incrementally, and be responsive to
      // changes in the data store rather than hooked up to a timer for for a
      // simple proof of concept this aught to do
      this.recomputeDimensions()

      const graph = this.graph

      this.workflows.forEach((workflow) => {
      })
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
              //...this.nodeDimensions[node.id],
              // the draw direction is LR so width and height are swapped
              width: this.nodeDimensions[node.id].height,
              height: this.nodeDimensions[node.id].width,
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

      dagre.layout(graph)

      var ele
      var graphNode
      graph.nodes().forEach(function (node) {
        graphNode = graph.node(node)
        ele = document.getElementById(graphNode.id)
        ele.style.top = `${graphNode.x}px`
        ele.style.left = `${graphNode.y}px`
      })
      graph.edges().forEach(function (v) {
      })

      console.log('done')
    }

  }

}
</script>

<style lang="scss">
.c-graph {
  .c-graph-node {
    position: absolute;
    top: 0px;
    left: 0px;
  }
}
</style>
