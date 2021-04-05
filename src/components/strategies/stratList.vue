<template>
  <div class="q-pa-md">
    <q-table
        v-if="stratrunners.length"
        :columns="stratColumns"
        row-key="id"
        :data="stratrunners"
        :pagination="{rowsPerPage: 0}"
        color="primary"
        separator="horizontal"
        hide-bottom
      >
        <q-tr slot="body" slot-scope="props" :props="props">
          <q-td key="type" :props="props">
            {{props.row.logType}}
          </q-td>
          <q-td key="lastupdate" :props="props">
            {{props.row.events.latest.candle ? props.row.events.latest.candle.start : '' | formatDate}}
          </q-td>
          <q-td class="bg-white" key="actions" :props="props">
            <q-btn size="sm" color="secondary" @click="$router.push(`live-gekkos/${props.row.id}`)" icon="visibility" label="view"/>
          </q-td>
        </q-tr>
      </q-table>
  </div>
</template>

<script>
  import moment from "moment";
  import humanizeDuration from "humanize-duration";
  import DateFilters from '../mixins/DateFilterMixin'

  export default {
    mixins: [DateFilters],
    data: () => {
      return {
        timer: false,
        now: moment(),
        stratColumns: [
          {name: "type", label: "Type"},
          {name: "lastupdate", label: "Last update"},
          {name: "actions", label: "Actions"}
        ]
      };
    },
    created: function () {
      this.timer = setInterval(() => {
        this.now = moment();
      }, 1000);
    },
    destroyed: function () {
      clearTimeout(this.timer);
    },
    computed: {
      stratrunners: function () {
        return _.values(this.$store.getters['gekkos/list'])
          .concat(_.values(this.$store.getters['gekkos/archive']))
          .filter(g => {
            switch (g.logType) {
              case 'papertrader':
                return true;
              case 'tradebot':
                return true;
              default:
                return false;
            }
          })
      }
    },
    methods: {
      status: state => {
        if (state.errored)
          return 'errored';
        if (state.stopped)
          return 'stopped';
        if (state.active)
          return 'running';

        console.log('unknown state:', state);
      },
      report: state => {
        return _.get(state, 'events.latest.performanceReport');
      },
      moment: mom => moment.utc(mom),
      round: n => (+n).toFixed(3),
      timespan: function (a, b) {
        return humanizeDuration(this.moment(a).diff(this.moment(b)));
      },
      successRate: rt => {
        if (!rt || !rt.length) return (0).toFixed(2) + " %"

        let successful = rt.filter(function (item) {
          return item.pnl > 0
        })
        return ((+successful.length / rt.length) * 100).toFixed(2) + " %"
      }
    }
  };
</script>