<template>
  <div class="card">
    <div class="flex items-center justify-between mb-4">
      <h3 class="text-lg font-semibold text-gray-900">{{ title }}</h3>
      <div v-if="showLegend" class="flex items-center space-x-4">
        <div v-for="item in legendItems" :key="item.label" class="flex items-center space-x-2">
          <div class="w-3 h-3 rounded-full" :style="{ backgroundColor: item.color }"></div>
          <span class="text-sm text-gray-600">{{ item.label }}</span>
        </div>
      </div>
    </div>
    <div class="h-64">
      <apexchart 
        :type="type" 
        height="250" 
        :options="chartOptions" 
        :series="series"
      ></apexchart>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue'
import VueApexCharts from 'vue3-apexcharts'

interface Props {
  title: string
  type: 'bar' | 'line' | 'donut'
  data: any
  showLegend?: boolean
  legendItems?: Array<{ label: string; color: string }>
}

const props = withDefaults(defineProps<Props>(), {
  showLegend: false,
  legendItems: () => []
})

const series = computed(() => {
  if (props.type === 'donut') {
    return props.data.datasets[0].data
  } else if (props.type === 'line') {
    return props.data.datasets.map((dataset: any) => ({
      name: dataset.label,
      data: dataset.data
    }))
  } else {
    return [{
      name: 'Valor',
      data: props.data.datasets[0].data
    }]
  }
})

const chartOptions = computed(() => {
  const baseOptions = {
    chart: {
      toolbar: {
        show: false
      },
      animations: {
        enabled: true,
        easing: 'easeinout',
        speed: 800
      }
    },
    tooltip: {
      enabled: true,
      style: {
        fontSize: '12px'
      }
    },
    legend: {
      show: false
    }
  }

  if (props.type === 'bar') {
    return {
      ...baseOptions,
      colors: props.data.datasets[0].backgroundColor || ['#3B82F6', '#10B981', '#F59E0B', '#EF4444', '#8B5CF6'],
      xaxis: {
        categories: props.data.labels,
        labels: {
          style: {
            fontSize: '12px'
          },
          rotate: -45
        }
      },
      yaxis: {
        labels: {
          style: {
            fontSize: '12px'
          }
        }
      },
      dataLabels: {
        enabled: true,
        style: {
          fontSize: '12px',
          colors: ['#fff']
        }
      },
      plotOptions: {
        bar: {
          borderRadius: 4,
          columnWidth: '60%'
        }
      }
    }
  } else if (props.type === 'line') {
    return {
      ...baseOptions,
      xaxis: {
        categories: props.data.labels,
        labels: {
          style: {
            fontSize: '12px'
          }
        }
      },
      yaxis: {
        labels: {
          style: {
            fontSize: '12px'
          }
        }
      },
      stroke: {
        width: 3,
        curve: 'smooth'
      },
      markers: {
        size: 6,
        hover: {
          size: 8
        }
      },
      fill: {
        type: 'gradient',
        gradient: {
          shade: 'light',
          type: 'vertical',
          shadeIntensity: 0.5,
          gradientToColors: ['#3B82F6', '#10B981'],
          inverseColors: false,
          opacityFrom: 0.8,
          opacityTo: 0.1,
          stops: [0, 100]
        }
      },
      colors: ['#3B82F6', '#10B981']
    }
  } else if (props.type === 'donut') {
    return {
      ...baseOptions,
      colors: props.data.datasets[0].backgroundColor || ['#3B82F6', '#10B981', '#F59E0B', '#EF4444', '#8B5CF6'],
      labels: props.data.labels,
      plotOptions: {
        pie: {
          donut: {
            size: '70%',
            labels: {
              show: true,
              total: {
                show: true,
                label: 'Total',
                fontSize: '16px',
                fontWeight: 'bold',
                color: '#374151'
              }
            }
          }
        }
      },
      dataLabels: {
        enabled: true,
        formatter: function(val: number) {
          return val.toFixed(1) + '%'
        },
        style: {
          fontSize: '12px',
          fontWeight: 'bold'
        }
      },
      legend: {
        position: 'bottom',
        fontSize: '12px',
        markers: {
          width: 8,
          height: 8,
          radius: 4
        }
      }
    }
  }

  return baseOptions
})
</script>

<script lang="ts">
export default {
  components: {
    apexchart: VueApexCharts
  }
}
</script>
