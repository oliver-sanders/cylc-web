<template>
  <v-container>
    <h1 style="margin:0;">Guide</h1>

    <!--
      TODO: make sections linkable

      TODO: add a contents bar which:
            * links to the guide sections
            * is presented when space is available
            * stays in a fixed position when scrolling (i.e. position: fixed)
            * tracks scroll position
    -->

    <h2 id="task-and-job-states">
      Task And Job States
    </h2>
    <!-- TODO document difference between tasks and jobs -->
    <div id="card-grid">

      <v-flex
        md6
        xs12
      >
        <v-card>
          <v-card-title primary-title>
            <p class="display-1 text--primary">Task State Vs Job State</p>
          </v-card-title>
          <v-card-text>
            <table id="task-job-state-table">
              <tr>
                <td>Task</td>
                <td></td>
                <td>Job</td>
              </tr>
              <tr>
                <td>
                  <p>
                    <!-- TODO - this better -->
                    The part which is related to the
                    workflow itself. These are states
                    which can be triggered off of
                  </p>
                </td>
                <td></td>
                <td>
                  <p>
                    <!-- TODO - this better -->
                    The status of a running job
                    which may or may not be the
                    same as the task status
                  </p>
                </td>
              </tr>
              <tr
                v-bind:key="state"
                v-for="state in states"
              >
                <td style="font-size: 2em;">
                  <task :status="state" progress="33" />
                </td>
                <td>
                  <span>{{ state }}</span>
                </td>
                <td style="font-size: 2em;">
                  <job :status="state" />
                </td>
              </tr>
            </table>
          </v-card-text>
        </v-card>
      </v-flex>

      <v-flex
        md5
        xs8
      >
        <v-card>
          <v-card-title primary-title>
            <p class="display-1 text--primary">Special Task States</p>
          </v-card-title>
          <v-card-text>
            <div id="task-job-special-grid">
              <div style="font-size: 2em">
                <task
                  status="waiting"
                  :isHeld="true"
                />
              </div>
              <div>
                <span>Held</span>
                <br />
                <span>
                  When a task is "held" no new job submissions will be made
                </span>
              </div>
            </div>
          </v-card-text>
        </v-card>
      </v-flex>
    </div>

    <v-divider></v-divider>

    <h2 id="task-and-job-states">
      Some Other Section
    </h2>

    <div id="card-grid">

      <v-flex
        md6
        xs12
      >
        <v-card>
          <v-card-title>
            <p class="display-1 text--primary">Foo</p>
          </v-card-title>
          <v-card-text>
            foo
          </v-card-text>
        </v-card>
      </v-flex>

    </div>

  </v-container>
</template>

<script>
import Task from '@/components/cylc/Task'
import Job from '@/components/cylc/Job'

export default {
  components: {
    task: Task,
    job: Job
  },
  // TODO: extract task states and descriptions from the GraphQL API
  //       once this is an enumeration.
  data: () => ({
    states: [
      'waiting',
      'submitted',
      'running',
      'succeeded',
      'failed',
      'submit-failed'
    ]
  })
}
</script>

<style lang="scss">
  @import '~@/styles/index.scss';

  #card-grid {
    display: flex;
    flex-wrap: wrap;
    margin-bottom: 1em;

    > * {
      margin: 0.5em;
    }

  }

  #task-job-state-table {
    text-align: center;
    /*border-spacing: 1em 0.5em;*/
    border-spacing: 0;

    p {
      font-size: 0.6em;
      line-height: 1.2em;
      /*color: $grey;*/
    }

    tr:nth-child(1) {
      font-size: 2em;
    }

    tr > td:nth-child(2) {
      font-size: 1em;
    }

    tr > td:nth-child(1), tr > td:nth-child(3) {
      width: 5em;
    }

    td {
      padding: 0.1em 0 0.1em 0;
    }

    td > * {
      background-color: white;
    }
  }

  #task-job-special-grid {
    font-size: 1.2em;
    display: flex;
    align-items: center;

    div:first-child {
      padding-right: 0.5em;
    }

    div > span:nth-child(3) {
      color: $grey;
    }
  }
</style>
