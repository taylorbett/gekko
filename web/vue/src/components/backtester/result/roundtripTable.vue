<template lang='jade'>
  .contain.roundtrips
    h2 Roundtrips
    .contain.sort-controls
      .grd-row
        .grd-row-col-3-6.mx1
          label(for='sortBy').wrapper Sort By:
          .custom-select.button
            select(v-model='sortBy')
              option(value='entryAt') Entry at
              option(value='exitAt') Exit at
              option(value='entryBalance') Entry balance
              option(value='exitBalance') Exit balance
              option(value='profit') Profit
              label(for='currency') Currency:
        .grd-row-col-3-6.mx1
          label(for='sortOrder').wrapper Order:  
          .custom-select.button
            select(v-model='sortOrder')
              option(value='descending') Descending
              option(value='ascending') Ascending
    table(v-if='roundtrips.length')
      thead
        tr
          th Row
          th Entry at
          th Exit at
          th Exposure
          th Entry balance
          th Exit balance
          th P&amp;L
          th Profit
        tr(v-for="(rt, index) in sort(roundtrips, sortBy, sortOrder)")
          td {{ index + 1 }}
          td {{ fmt(rt.entryAt) }}
          td {{ fmt(rt.exitAt) }}
          td {{ diff(rt.duration) }}
          td {{ round(rt.entryBalance) }} 
            span.font-secondary {{ report.currency }}
          td {{ round(rt.exitBalance) }} 
            span.font-secondary {{ report.currency }}
          template(v-if="Math.sign(rt.pnl)===-1")
            td.loss {{ Math.sign(rt.pnl)*rt.pnl.toFixed(2) }} 
              span.font-secondary {{ report.currency }}
            td.loss {{ rt.profit.toFixed(2) }} 
              span.font-secondary %
          template(v-else)
            td.profit {{ rt.pnl.toFixed(2) }} 
              span.font-secondary {{ report.currency }}
            td.profit {{ rt.profit.toFixed(2) }} 
              span.font-secondary %
    div(v-if='!roundtrips.length')
      p Not enough data to display
</template>

<script>
export default {
  props: ['roundtrips', 'report'],
  data: () => {
    return {
      sortBy: 'entryAt',
      sortOrder: 'ascending',
    }
  },
  methods: {
    diff: n => moment.duration(n).humanize(),
    humanizeDuration: (n) => window.humanizeDuration(n),
    fmt: mom => moment.utc(mom).format('YYYY-MM-DD HH:mm'),
    round: n => (+n).toFixed(2),
    sort: (data, key, order) => {
      if (!key) {
        return data;
      }
      return data.slice().sort(function (a, b) {
        let itemA = a[key];
        let itemB = b[key];
        if (key === 'entryAt' || key === 'exitAt') {
          itemA = moment(itemA).utc();
          itemB = moment(itemB).utc();
        }
        return order === 'ascending' ? (itemA - itemB) : (itemB - itemA);
      });
    },
  },
}
</script>

<style>

.roundtrips {
  margin-top: 50px;
  margin-bottom: 50px;
}

.sort-controls {
  margin-bottom: 18px;
}

.roundtrips table {
  width: 100%;
}

.roundtrips table th,
.roundtrips table td {
  border: 1px solid #c6cbd1;
  padding: 8px 10px;
}

.roundtrips table td.loss {
  color: red;
}
.roundtrips table td.profit {
  color: #2ecc71;
}

.roundtrips table td:last-child {
  text-align: right;
}

.roundtrips table td .font-secondary {
  color: #999;
}

.roundtrips table tr:nth-child(2n) {
  background-color: #f6f8fa;
}

</style>
