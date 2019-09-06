<template>
  <v-container grid-list-lg>
    <p class="display-3 font-weight-thin mb-3">Guide</p>

    <h2 class="font-weight-thin mb-3">Task And Job States</h2>

    <!--
      TODO: make sections linkable

      TODO: add a contents bar which:
            * links to the guide sections
            * is presented when space is available
            * stays in a fixed position when scrolling (i.e. position: fixed)
            * tracks scroll position
    -->
    <!-- TODO document difference between tasks and jobs -->
    <v-layout row wrap>
      <v-flex xs12 md12 lg6>
        <v-card class="mx-auto" flat elevation="1">
          <v-card-title>
            <p class="display-1 text--primary">Task State Vs Job State</p>
          </v-card-title>
          <v-card-text>
            <v-data-table
                hide-actions
                disable-initial-sort
                :items="states"
                :headers="headers">
              <template v-slot:no-data><span></span></template>
              <template v-slot:no-results><span></span></template>
              <template v-slot:headers="props">
                <tr>
                  <th
                    v-for="header in props.headers"
                    :key="header.text"
                  >
                    {{ header.text }}
                  </th>
                </tr>
                <tr>
                  <td
                      v-for="header in props.headers"
                      :key="header.subtitle"
                      class="text-gray body-1"
                  >
                    {{ header.subtitle }}
                  </td>
                </tr>
              </template>
              <template v-slot:items="props">
                <td class="text-xs-center">
                  <task :status="props.item.text" :progress="33" class="headline" />
                </td>
                <td class="text-xs-center">{{ props.item.text }}</td>
                <td class="text-xs-center">
                  <job :status="props.item.text" class="headline" />
                </td>
              </template>
            </v-data-table>
          </v-card-text>
        </v-card>
      </v-flex>
      <v-flex xs12 md12 lg6>
        <v-card class="mx-auto" flat elevation="1">
          <v-card-title>
            <p class="display-1 text--primary">Special Task States</p>
          </v-card-title>
          <v-card-text class="my-0 py-0">
            <v-list three-line>
              <v-list-tile v-for="specialState in special" :key="specialState.text">
                <v-list-tile-avatar>
                  <task
                    :status="specialState.status"
                    :isHeld="specialState.isHeld"
                    class="headline"
                  />
                </v-list-tile-avatar>
                <v-list-tile-content>
                  <v-list-tile-title v-html="specialState.text"></v-list-tile-title>
                  <v-list-tile-sub-title v-html="specialState.description"></v-list-tile-sub-title>
                </v-list-tile-content>
              </v-list-tile>
            </v-list>
          </v-card-text>
        </v-card>
      </v-flex>
    </v-layout>

    <v-divider class="my-1"></v-divider>

    <h2 class="font-weight-thin mb-3">Some Other Section</h2>

    <v-flex xs12 md12 lg6>
      <v-card class="mx-auto" flat elevation="1">
        <v-card-title>
          <p class="display-1 text--primary">Foo</p>
        </v-card-title>
        <v-card-text>
          foo
        </v-card-text>
      </v-card>
    </v-flex>
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
    headers: [
      {
        text: 'Task',
        subtitle: 'The part which is related to the workflow itself. These are states which can be triggered off of',
        align: 'center',
        value: 'text',
        sortable: false
      },
      {
        text: '',
        sortable: false
      },
      {
        text: 'Job',
        subtitle: 'The status of a running job which may or may not be the same as the task status',
        align: 'center',
        value: 'text',
        sortable: false
      }
    ],
    states: [
      { text: 'waiting' },
      { text: 'submitted' },
      { text: 'running' },
      { text: 'succeeded' },
      { text: 'failed' },
      { text: 'submit-failed' }
    ],
    special: [
      {
        text: 'Held',
        description: 'When a task is "held" no new job submissions will be made',
        status: 'waiting',
        isHeld: true
      }
    ]
  })
}
</script>
