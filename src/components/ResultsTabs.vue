<template>

<div>
  <div class="row">
    <div class="col-lg">
        <apexchart ref="pressChart" width="500" type="line" :options="pressChartOptions" :series="pressSeries"></apexchart>
        <apexchart ref="prodChart" width="500" type="line" :options="prodChartOptions" :series="prodSeries"></apexchart>
    </div>
    <div class="col-lg">
        <apexchart ref="pzChart" width="500" :options="pzChartOptions" :series="pzSeries"></apexchart> 
        <p>OGIP Estimate from P/Z plot is {{ pzOGIP }} MMscf</p>
    </div>
    
</div>
</div>


</template>

<script>
import VueApexCharts from 'vue3-apexcharts'

export default {
    name: 'ResultsTabs',

    components: {
      'apexchart': VueApexCharts
    },
    props: ['pressureData', 'productionData', 'pzData','pzRegress', 'pzOGIP'],
    watch: {
        pressureData(newValue) {
            this.pressSeries = [{name: "Pressure", data: newValue}]
        },
        productionData(newValue) {
            this.prodSeries = [{name: "Production", data: newValue}]
        },
        pzData(newValue) {
            this.pzSeries = [{name: "PZ_scatter", data: newValue}]
        },
    },
    data() { return {
        pressSeries: [{name: "Pressure", data: this.pressureData}],
        pressChartOptions: {
            chart: {
              type: 'line',
              height: 500,
              zoom: {
                type: 'x',
                enabled: true,
                autoScaleYaxis: true
              },
              toolbar: {
                autoSelected: 'zoom'
              }
            },
            dataLabels: {
              enabled: false
            },
            markers: {
              size: 0,
            },
            title: {
              text: 'Pressure Profile',
              align: 'left'
            },
            yaxis: {
              labels: {
                formatter: function (val) {
                  return (val / 1).toFixed(0);
                },
              },
              title: {
                text: 'Pressure (psia)'
              },
            },
            tooltip: {
              shared: false,
              y: {
                formatter: function (val) {
                  return (val / 1).toFixed(0)
                }
              }
            }
          },
          prodSeries: [{name: "Production", data: this.productionData}],
          prodChartOptions: {
            chart: {
              type: 'line',
              height: 500,
              zoom: {
                type: 'x',
                enabled: true,
                autoScaleYaxis: true
              },
              toolbar: {
                autoSelected: 'zoom'
              }
            },
            dataLabels: {
              enabled: false
            },
            markers: {
              size: 0,
            },
            title: {
              text: 'Production Profile',
              align: 'left'
            },
            yaxis: {
              labels: {
                formatter: function (val) {
                  return (val / 1).toFixed(0);
                },
              },
              title: {
                text: 'Production (mscf)'
              },
            },
            tooltip: {
              shared: false,
              y: {
                formatter: function (val) {
                  return (val / 1).toFixed(0)
                }
              }
            }
          },
          pzSeries: [{name: "PZ_scatter",
                      type: "scatter",
                      data: this.pzData}],
          pzChartOptions: {
            chart: {
              height: 350,
              type: 'scatter',
            },
            title: {
              text: 'P/Z Plot',
              align: 'left'
            },
            markers: {
              size: [6, 0]
            },
            legend: {
              show: false
            },
            yaxis: {
              labels: {
                formatter: function (val) {
                  return (val / 1).toFixed(0);
                },
              },
              title: {
                text: 'P/Z'
              },
            },
              xaxis: {
              labels: {
                formatter: function (val) {
                  return (val / 1).toFixed(0);
                },
              },
              title: {
                text: 'Cumulative Production (mscf)'
              }
            }
          }
        }
    },
    methods: { checkData : function () {
                          console.log(this.series)}
    }
}       
    
</script>