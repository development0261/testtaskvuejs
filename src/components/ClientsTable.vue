<template>
  <table>
    <thead>
    <tr>
      <th>Date</th>
      <th>Symbol</th>
      <th>Open</th>
      <th>High</th>
      <th>Low</th>
      <th>Close</th>
    </tr>
    </thead>
    <tbody>
    <tr v-for="data in itemsPaginated" :key="data.date">
      <td data-label="Date">{{ data.date }}</td>
      <td data-label="Symbol">{{ data.symbol }}</td>
      <td data-label="Open">{{ data.open }}</td>
      <td data-label="High">{{ data.high }}</td>
      <td data-label="Low">{{ data.low }}</td>
      <td data-label="Close">{{ data.close }}</td>
    </tr>
    </tbody>
  </table>
  <div class="table-pagination">
    <div class="level">
      <div class="level-left">
        <div class="level-item">
          <div class="buttons">
            <button v-for="page in pagesList"
                    @click="currentPage = page"
                    type="button" class="button"
                    :class="{'active': page===currentPage}"
                    :key="page">{{ page + 1 }}</button>
          </div>
        </div>
      </div>
      <div class="level-right">
        <div class="level-item">
          <small>Page {{ currentPageHuman }} of {{ numPages }}</small>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { computed, ref } from 'vue'
import { mdiEye, mdiTrashCan } from '@mdi/js'
import axios from 'axios'
import slice from 'lodash/slice'
import remove from 'lodash/remove'

export default {
  name: 'ClientsTable',
  props: {
    checkable: {
      type: Boolean,
      default: false
    }
  },
  setup () {
    const isModalActive = ref(false)

    const isLoading = ref(false)

    const items = ref([])

    const perPage = ref(10)

    const currentPage = ref(0)

    const checkedRows = ref([])

    const itemsPaginated = computed(
      () => slice(items.value, perPage.value * currentPage.value, perPage.value * (currentPage.value + 1))
    )

    const numPages = computed(() => Math.ceil(items.value.length / perPage.value))

    const currentPageHuman = computed(() => currentPage.value + 1)

    const pagesList = computed(() => {
      const pagesList = []

      for (let i = 0; i < numPages.value; i++) {
        pagesList.push(i)
      }

      return pagesList
    })

    const checked = (isChecked, client) => {
      if (isChecked) {
        checkedRows.value.push(client)
      } else {
        remove(checkedRows.value, row => row.id === client.id)
      }
    }

    // Fetch sample data

    isLoading.value = true

    axios
      .get('https://www.alphavantage.co/query?function=TIME_SERIES_DAILY&symbol=IBM&apikey=demo')
      .then((r) => {
        const symbol = r.data.['Meta Data']['2. Symbol']
        const data = r.data.['Time Series (Daily)']
        // console.log('data', data)
        // console.log('symbol', symbol)
        let temp
        for (const d in data) {
          temp = { date: d, symbol: symbol, open: data[d]['1. open'], high: data[d]['2. high'], low: data[d]['3. low'], close: data[d]['4. close'] }
          items.value.push(temp)
        }
        isLoading.value = false
      })
      .catch(error => {
        isLoading.value = false
        alert(error.message)
      })

    return {
      isModalActive,
      currentPage,
      currentPageHuman,
      numPages,
      checkedRows,
      itemsPaginated,
      pagesList,
      checked,
      mdiEye,
      mdiTrashCan
    }
  }
}
</script>
