<template>
  <div class="min-h-screen bg-gray-100 p-6">
    <h1 class="text-2xl font-bold mb-6">Dashboard</h1>
    
    <!-- Date Filter -->
    <div class="mb-6">
      <h2 class="text-lg font-medium mb-3">Periode</h2>
      <div class="flex items-center">
        <div class="relative flex items-center border rounded-lg p-2 bg-white w-64">
          <CalendarIcon class="h-5 w-5 text-gray-500 mr-2" />
          <input 
            type="text" 
            v-model="dateRangeText" 
            class="bg-transparent outline-none flex-1"
            readonly
            @click="showDatePicker = !showDatePicker"
          />
          <div v-if="showDatePicker" class="absolute top-full left-0 mt-1 bg-white shadow-lg rounded-lg p-4 z-10">
            <div class="flex gap-4">
              <div>
                <div class="mb-2 font-medium">Dari:</div>
                <select v-model="startMonth" class="border p-1 rounded">
                  <option v-for="(month, index) in months" :key="`start-${index}`" :value="index">{{ month }}</option>
                </select>
                <select v-model="startYear" class="border p-1 rounded ml-2">
                  <option v-for="year in years" :key="`start-${year}`" :value="year">{{ year }}</option>
                </select>
              </div>
              <div>
                <div class="mb-2 font-medium">Sampai:</div>
                <select v-model="endMonth" class="border p-1 rounded">
                  <option v-for="(month, index) in months" :key="`end-${index}`" :value="index">{{ month }}</option>
                </select>
                <select v-model="endYear" class="border p-1 rounded ml-2">
                  <option v-for="year in years" :key="`end-${year}`" :value="year">{{ year }}</option>
                </select>
              </div>
            </div>
            <div class="mt-3 flex justify-end">
              <button 
                @click="applyDateRange" 
                class="bg-blue-500 text-white px-3 py-1 rounded text-sm"
              >
                Terapkan
              </button>
            </div>
          </div>
        </div>
        <button 
          @click="fetchData" 
          class="ml-4 bg-blue-500 text-white px-6 py-2 rounded-lg hover:bg-blue-600 transition"
        >
          Lihat
        </button>
      </div>
    </div>
    
    <!-- Charts Grid -->
    <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
      <!-- Wheelchair Status Distribution -->
      <div class="bg-white p-6 rounded-lg shadow-sm">
        <h3 class="text-lg font-medium mb-4">Distribusi Status Kursi Roda</h3>
        <div class="h-64 flex items-center justify-center">
          <div class="relative h-48 w-48">
            <div class="absolute inset-0 flex items-center justify-center">
              <div class="h-24 w-24 bg-white rounded-full"></div>
            </div>
            <canvas ref="wheelchairStatusChart"></canvas>
          </div>
          <div class="ml-6 space-y-2">
            <div class="flex items-center">
              <div class="h-4 w-4 bg-blue-500 rounded mr-2"></div>
              <span>Tersedia</span>
            </div>
            <div class="flex items-center">
              <div class="h-4 w-4 bg-gray-400 rounded mr-2"></div>
              <span>Disewakan</span>
            </div>
            <div class="flex items-center">
              <div class="h-4 w-4 bg-green-400 rounded mr-2"></div>
              <span>Dalam perbaikan</span>
            </div>
          </div>
        </div>
      </div>
      
      <!-- Agent Performance -->
      <div class="bg-white p-6 rounded-lg shadow-sm">
        <div class="flex justify-between items-center mb-4">
          <h3 class="text-lg font-medium">Performa Agen</h3>
          <div class="flex items-center">
            <span class="text-sm text-gray-500 mr-2">Bulan</span>
            <ChevronDownIcon class="h-4 w-4 text-gray-500" />
          </div>
        </div>
        <div class="h-64 flex items-center justify-center">
          <canvas ref="agentPerformanceChart"></canvas>
        </div>
      </div>
      
      <!-- Wheelchair Usage Trend -->
      <div class="bg-white p-6 rounded-lg shadow-sm">
        <h3 class="text-lg font-medium mb-4">Tren Penggunaan Kursi Roda</h3>
        <div class="h-64 relative">
          <div class="absolute top-0 right-0 bg-blue-500 text-white text-xs px-2 py-1 rounded">
            Tipe A-48
          </div>
          <canvas ref="usageTrendChart"></canvas>
        </div>
      </div>
      
      <!-- Total Transactions -->
      <div class="bg-white p-6 rounded-lg shadow-sm">
        <div class="flex justify-between items-center mb-4">
          <h3 class="text-lg font-medium">Total Transaksi</h3>
        </div>
        <div class="h-64 relative">
          <canvas ref="transactionsChart"></canvas>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive, onMounted, computed } from 'vue'
import { 
  CalendarIcon,
  ChevronDownIcon
} from 'lucide-vue-next'
import Chart from 'chart.js/auto'

// Chart references
const wheelchairStatusChart = ref(null)
const agentPerformanceChart = ref(null)
const usageTrendChart = ref(null)
const transactionsChart = ref(null)

// Chart instances
const wheelchairStatusChartInstance = ref(null)
const agentPerformanceChartInstance = ref(null)
const usageTrendChartInstance = ref(null)
const transactionsChartInstance = ref(null)

// Date picker state
const showDatePicker = ref(false)
const months = [
  'Januari', 'Februari', 'Maret', 'April', 'Mei', 'Juni',
  'Juli', 'Agustus', 'September', 'Oktober', 'November', 'Desember'
]
const currentYear = new Date().getFullYear()
const years = [currentYear - 2, currentYear - 1, currentYear, currentYear + 1, currentYear + 2]
const startMonth = ref(2) // Maret
const startYear = ref(2025)
const endMonth = ref(2) // Maret
const endYear = ref(2025)

// Computed date range text
const dateRangeText = computed(() => {
  return `1 ${months[startMonth.value]} ${startYear.value} - 31 ${months[endMonth.value]} ${endYear.value}`
})

// Apply date range and close picker
const applyDateRange = () => {
  showDatePicker.value = false
}

// Dashboard data
const dashboardData = reactive({
  wheelchairStatus: {
    tersedia: 50,
    disewakan: 25,
    dalamPerbaikan: 25
  },
  agentPerformance: {
    'Agen A': 34,
    'Agen B': 30,
    'Agen C': 36
  },
  usageTrend: {
    'Senin': 30,
    'Selasa': 35,
    'Rabu': 48,
    'Kamis': 40,
    'Jumat': 35,
    'Sabtu': 45,
    'Minggu': 50
  },
  totalTransactions: {
    data: [80, 105, 90, 80, 70, 90],
    labels: ['Oktober', 'November', 'Desember', 'Januari', 'Februari', 'Maret'],
    highlight: {
      month: 'November',
      year: 2024,
      value: 107
    }
  }
})

// Generate random data based on date range
const generateRandomData = () => {
  // Seed for consistent random data based on parameters
  const seed = startMonth.value + startYear.value + endMonth.value + endYear.value
  const random = (min, max, seed) => {
    const x = Math.sin(seed) * 10000
    const r = x - Math.floor(x)
    return Math.floor(min + r * (max - min + 1))
  }
  
  // Generate wheelchair status data
  const tersedia = random(40, 60, seed)
  const disewakan = random(15, 30, seed + 1)
  const dalamPerbaikan = 100 - tersedia - disewakan
  
  // Generate agent performance data
  const agenA = random(30, 40, seed + 2)
  const agenB = random(25, 35, seed + 3)
  const agenC = 100 - agenA - agenB
  
  // Generate usage trend data
  const usageTrend = {
    'Senin': random(25, 40, seed + 4),
    'Selasa': random(30, 45, seed + 5),
    'Rabu': random(35, 50, seed + 6),
    'Kamis': random(30, 45, seed + 7),
    'Jumat': random(25, 40, seed + 8),
    'Sabtu': random(35, 50, seed + 9),
    'Minggu': random(40, 55, seed + 10)
  }
  
  // Generate transaction data for 6 months
  const transactionData = []
  const transactionLabels = []
  
  // Calculate 6 months before end date
  let month = endMonth.value - 5
  let year = endYear.value
  
  while (month < 0) {
    month += 12
    year -= 1
  }
  
  for (let i = 0; i < 6; i++) {
    transactionLabels.push(months[month])
    
    const value = random(60, 110, seed + month + year)
    transactionData.push(value)
    
    month++
    if (month >= 12) {
      month = 0
      year++
    }
  }
  
  // Generate highlight month (November 2024 in the example)
  const highlightMonth = 'November'
  const highlightYear = 2024
  const highlightValue = 107
  
  return {
    wheelchairStatus: {
      tersedia,
      disewakan,
      dalamPerbaikan
    },
    agentPerformance: {
      'Agen A': agenA,
      'Agen B': agenB,
      'Agen C': agenC
    },
    usageTrend,
    totalTransactions: {
      data: transactionData,
      labels: transactionLabels,
      highlight: {
        month: highlightMonth,
        year: highlightYear,
        value: highlightValue
      }
    }
  }
}

// Fetch data based on filters
const fetchData = () => {
  const newData = generateRandomData()
  
  // Update reactive data
  dashboardData.wheelchairStatus = newData.wheelchairStatus
  dashboardData.agentPerformance = newData.agentPerformance
  dashboardData.usageTrend = newData.usageTrend
  dashboardData.totalTransactions = newData.totalTransactions
  
  // Update charts
  updateWheelchairStatusChart()
  updateAgentPerformanceChart()
  updateUsageTrendChart()
  updateTransactionsChart()
}

// Create percentage labels plugin
const createPercentageLabelsPlugin = () => {
  return {
    id: 'percentageLabels',
    afterDraw: (chart) => {
      const ctx = chart.ctx
      chart.data.datasets[0].data.forEach((value, index) => {
        const meta = chart.getDatasetMeta(0)
        const arc = meta.data[index]
        const centerX = arc.x
        const centerY = arc.y
        const angle = arc.startAngle + (arc.endAngle - arc.startAngle) / 2
        const radius = arc.outerRadius * 0.7
        
        // Calculate position
        const x = centerX + Math.cos(angle) * radius
        const y = centerY + Math.sin(angle) * radius
        
        // Draw text
        ctx.save()
        ctx.fillStyle = 'white'
        ctx.font = 'bold 16px Arial'
        ctx.textAlign = 'center'
        ctx.textBaseline = 'middle'
        ctx.fillText(value + '%', x, y)
        ctx.restore()
      })
    }
  }
}

// Create total transactions label plugin
const createTotalTransactionsPlugin = () => {
  return {
    id: 'totalTransactionsLabel',
    afterDraw: (chart) => {
      const ctx = chart.ctx
      ctx.save()
      ctx.font = '12px Arial'
      ctx.fillStyle = '#6b7280'
      ctx.textAlign = 'center'
      ctx.fillText(
        `${dashboardData.totalTransactions.highlight.month} ${dashboardData.totalTransactions.highlight.year}`, 
        chart.width / 2, 
        15
      )
      ctx.font = 'bold 12px Arial'
      ctx.fillText(
        dashboardData.totalTransactions.highlight.value, 
        chart.width / 2, 
        30
      )
      ctx.restore()
    }
  }
}

// Update wheelchair status chart
const updateWheelchairStatusChart = () => {
  if (wheelchairStatusChartInstance.value) {
    wheelchairStatusChartInstance.value.data.datasets[0].data = [
      dashboardData.wheelchairStatus.tersedia,
      dashboardData.wheelchairStatus.disewakan,
      dashboardData.wheelchairStatus.dalamPerbaikan
    ]
    wheelchairStatusChartInstance.value.update()
  } else if (wheelchairStatusChart.value) {
    wheelchairStatusChartInstance.value = new Chart(wheelchairStatusChart.value, {
      type: 'doughnut',
      data: {
        labels: ['Tersedia', 'Disewakan', 'Dalam perbaikan'],
        datasets: [{
          data: [
            dashboardData.wheelchairStatus.tersedia,
            dashboardData.wheelchairStatus.disewakan,
            dashboardData.wheelchairStatus.dalamPerbaikan
          ],
          backgroundColor: ['#3b82f6', '#9ca3af', '#4ade80'],
          borderWidth: 0,
          cutout: '70%'
        }]
      },
      options: {
        responsive: true,
        maintainAspectRatio: false,
        plugins: {
          legend: {
            display: false
          },
          tooltip: {
            enabled: true
          }
        }
      },
      plugins: [createPercentageLabelsPlugin()]
    })
  }
}

// Update agent performance chart
const updateAgentPerformanceChart = () => {
  if (agentPerformanceChartInstance.value) {
    agentPerformanceChartInstance.value.data.datasets[0].data = [
      dashboardData.agentPerformance['Agen A'],
      dashboardData.agentPerformance['Agen B'],
      dashboardData.agentPerformance['Agen C']
    ]
    agentPerformanceChartInstance.value.update()
  } else if (agentPerformanceChart.value) {
    agentPerformanceChartInstance.value = new Chart(agentPerformanceChart.value, {
      type: 'pie',
      data: {
        labels: ['Agen A', 'Agen B', 'Agen C'],
        datasets: [{
          data: [
            dashboardData.agentPerformance['Agen A'],
            dashboardData.agentPerformance['Agen B'],
            dashboardData.agentPerformance['Agen C']
          ],
          backgroundColor: ['#4ade80', '#f97316', '#9ca3af'],
          borderWidth: 0
        }]
      },
      options: {
        responsive: true,
        maintainAspectRatio: false,
        plugins: {
          legend: {
            position: 'right',
            labels: {
              usePointStyle: true,
              padding: 20
            }
          }
        }
      },
      plugins: [createPercentageLabelsPlugin()]
    })
  }
}

// Update usage trend chart
const updateUsageTrendChart = () => {
  if (usageTrendChartInstance.value) {
    usageTrendChartInstance.value.data.datasets[0].data = Object.values(dashboardData.usageTrend)
    usageTrendChartInstance.value.update()
  } else if (usageTrendChart.value) {
    usageTrendChartInstance.value = new Chart(usageTrendChart.value, {
      type: 'line',
      data: {
        labels: Object.keys(dashboardData.usageTrend),
        datasets: [{
          label: 'Penggunaan',
          data: Object.values(dashboardData.usageTrend),
          borderColor: '#3b82f6',
          tension: 0.3,
          fill: false
        }]
      },
      options: {
        responsive: true,
        maintainAspectRatio: false,
        scales: {
          y: {
            beginAtZero: true,
            max: 60,
            ticks: {
              stepSize: 10
            }
          }
        },
        plugins: {
          legend: {
            display: false
          }
        }
      },
      plugins: [{
        id: 'typeLabel',
        afterDraw: (chart) => {
          const ctx = chart.ctx
          ctx.save()
          
          // Draw blue background
          ctx.fillStyle = '#3b82f6'
          ctx.beginPath()
          ctx.roundRect(chart.width - 70, 10, 60, 20, 4)
          ctx.fill()
          
          // Draw text
          ctx.fillStyle = 'white'
          ctx.font = '12px Arial'
          ctx.textAlign = 'center'
          ctx.textBaseline = 'middle'
          ctx.fillText('Tipe A-48', chart.width - 40, 20)
          ctx.restore()
        }
      }]
    })
  }
}

// Update transactions chart
const updateTransactionsChart = () => {
  if (transactionsChartInstance.value) {
    transactionsChartInstance.value.data.labels = dashboardData.totalTransactions.labels
    transactionsChartInstance.value.data.datasets[0].data = dashboardData.totalTransactions.data
    transactionsChartInstance.value.update()
  } else if (transactionsChart.value) {
    transactionsChartInstance.value = new Chart(transactionsChart.value, {
      type: 'bar',
      data: {
        labels: dashboardData.totalTransactions.labels,
        datasets: [{
          label: 'Transaksi',
          data: dashboardData.totalTransactions.data,
          backgroundColor: '#3b82f6',
          borderRadius: 4
        }]
      },
      options: {
        responsive: true,
        maintainAspectRatio: false,
        scales: {
          y: {
            beginAtZero: true,
            max: 120,
            ticks: {
              stepSize: 20
            }
          }
        },
        plugins: {
          legend: {
            display: false
          },
          title: {
            display: false
          }
        }
      },
      plugins: [createTotalTransactionsPlugin()]
    })
  }
}

// Initialize charts on mount
onMounted(() => {
  updateWheelchairStatusChart()
  updateAgentPerformanceChart()
  updateUsageTrendChart()
  updateTransactionsChart()
})
</script>