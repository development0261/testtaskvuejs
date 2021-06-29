<template>
  <title-bar :title-stack="titleStack" />
  <main-section>
    <tiles>
      <card-widget
        class="tile"
        color="text-green-500"
        :icon="mdiAccountMultiple"
        :number="2158"
        label="Clicks (+ 64.2%)"
      />
      <card-widget
        class="tile"
        color="text-blue-500"
        :icon="mdiCartOutline"
        :number="1.8"
        prefix="$"
        suffix="M"
        label="Impressions (+ 78%)"
      />
      <card-widget
        class="tile"
        color="text-red-500"
        :icon="mdiChartTimelineVariant"
        :number=".12"
        suffix="%"
        label="AVERAGE CTR (+ 82%)"
      />
    </tiles>

    <card-component :icon="mdiShareVariant" title="Graph : Table Title" has-table>
      <clients-table />
    </card-component>
    <card-component
      title="Table Title"
      :icon="mdiShareVariant"
      class="mb-6"
      @header-icon-click="fillChartData"
    >
      <div v-if="chartData" class="chart-area">
        <candle-stick></candle-stick>
      </div>
    </card-component>
  </main-section>
</template>

<script>
// @ is an alias to /src
import { ref, onMounted } from 'vue'
import {
  mdiAccountMultiple,
  mdiCartOutline,
  mdiChartTimelineVariant,
  mdiShareVariant,
  mdiMonitorCellphone,
  mdiReload
} from '@mdi/js'
import * as chartConfig from '@/components/Charts/chart.config'
import CandleStick from '@/components/Charts/CandleStick'
import MainSection from '@/components/MainSection'
import TitleBar from '@/components/TitleBar'
import Tiles from '@/components/Tiles'
import CardWidget from '@/components/CardWidget'
import CardComponent from '@/components/CardComponent'
import ClientsTable from '@/components/ClientsTable'

export default {
  name: 'Home',
  components: {
    MainSection,
    ClientsTable,
    CardComponent,
    CardWidget,
    Tiles,
    TitleBar,
    CandleStick
  },
  setup () {
    const titleStack = ref(['Admin', 'Dashboard'])

    const chartOptions = chartConfig.chartOptions

    const chartData = ref(null)

    const fillChartData = () => {
      chartData.value = chartConfig.sampleChartData()
    }

    onMounted(() => {
      fillChartData()
    })

    return {
      titleStack,
      chartOptions,
      chartData,
      fillChartData,
      mdiAccountMultiple,
      mdiCartOutline,
      mdiChartTimelineVariant,
      mdiShareVariant,
      mdiMonitorCellphone,
      mdiReload
    }
  }
}
</script>
