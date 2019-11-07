<template>
  <span
    class="c-job-box"
    style="display:inline-block; vertical-align:middle"
  >
    <template
      v-for="job in jobProxies"
    >
      <job
        :status="job.state"
        :number="job.number"
      />
    </template>
  </span>
</template>

<script>
import Job from '@/components/cylc/Job'

export default {
  name: 'JobBox',
  components: {
    job: Job
  },
  props: {
    stateTotals: {
      type: Object,
      required: false
    },
    jobs: {
      type: Array,
      required: false
    },
    maxItems: {
      type: Number,
      required: false,
      default: 2
    }
  },

  computed: {
    jobProxies: function () {
      var stateTotals
      if (this.jobs) {
        if (this.jobs.length < this.maxItems) {
          return this.jobs
        }
        stateTotals = this.getStateTotals(this.jobs)
      } else {
        stateTotals = this.stateTotals
      }
      const ret = []
      for (let stat in stateTotals) {
        ret.push({
          'state': stat,
          'number': stateTotals[stat]
        })
      }
      console.log('#', stateTotals, ret)
      return ret
    }
  },

  methods: {
    getStateTotals: function (jobs) {
      const stateTotals = {}
      for (let job of jobs) {
        stateTotals[job.state] = 0
      }
      for (let job of jobs) {
        stateTotals[job.state] += 1
      }
      return stateTotals
    }
  }
}
</script>
