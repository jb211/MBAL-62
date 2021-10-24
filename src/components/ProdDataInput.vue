<template>
<div>
     <p>Paste reservoir data straight from excel into the text area below:</p>  
     <p>Use the format [Date, Pressure (psia), Production (mscf)]. Please paste without the column headers.</p>  
        <textarea v-model="ProdDataRaw" name="prod-data" style="width:250px;height:150px;"></textarea><br>
        <button class="btn btn-primary mr-25" v-on:click="generateJSON">Update Production Data</button>
        <button class="btn btn-primary" v-on:click="generatePZPlot">Generate MBAL Plots + Results</button>
        <ResultsTab 
            :pressureData="ResultsData.pressureData" 
            :productionData="ResultsData.productionData" 
            :pzData="ResultsData.PzData"
            :pzRegress="PzRegression"
            :pzOGIP="PzOGIP"
            :feData="ResultsData.FeData"
            :feRegress="FeRegression"
            :feOGIP="FeOGIP"
            :pzLine="ResultsData.PzLine"
            :feLine="ResultsData.FeLine"
        />
</div>
</template>

<script>
import ResultsTab from './ResultsTabs'

export default {
    name: 'ProdDataInput',
    components: {
      'ResultsTab': ResultsTab
    },
    data() { return {
        ProdDataRaw: [],
        ResultsData: {
           pressureData: [],
           productionData: [],
           PzData : [],
           FeData : [],
           PzLine : [],
           FeLine : []
        },
        PzRegression : [],
        PzOGIP : 0,
        FeRegression: [],
        FeOGIP: 0
    }
    },
    methods: { generateJSON : function () {
                var data = this.ProdDataRaw
                function split_entry(x) {
                        var str_ls = x.split('\t')
                    return {
                        "Date": str_ls[0],
                        "Pressure": parseFloat(str_ls[1]),
                        "Production": parseFloat(str_ls[2])
                    }
                }
                var result = data.trim().split('\n').map(s => split_entry(s))
                var pressure_data = result.map( (e) => {return {x: e.Date, y: e.Pressure}})
                var production_data = result.map( (e) => {return {x: e.Date, y: e.Production}})
                this.ResultsData.pressureData = pressure_data
                this.ResultsData.productionData = production_data
                return result
            
        },
         generatePZPlot : async function () {
                var data = this.ProdDataRaw
                function split_entry(x) {
                        var str_ls = x.split('\t')
                    return {
                        "Date": str_ls[0],
                        "Pressure(psia)": str_ls[1],
                        "Production (mmscf)": str_ls[2]
                    }
                }
                var result = data.trim().split('\n').map(s => split_entry(s))
                const requestOptions = {
                    method: "POST",
                    headers: { "Content-Type": 'application/json' },
                    body: JSON.stringify({ "resTemp":343,
                            "resGrav": 0.66,
                            "resData": result})
                };
                const resp = await fetch("https://o03x5atg21.execute-api.us-east-1.amazonaws.com/default/gas_MBAL_calculations", requestOptions)
                var res = await resp.json()
                var bod = JSON.parse(res["body"])
                console.log(bod)
                var pz_plot_data = bod.data_set.map( (e) => {return {x: parseFloat(e["Production (mmscf)"]), y: e["P/Z"]}})
                
                var fe_plot_data = bod.data_set.map( (e) => {return {x: e["Eg"], y: e["Bg"] * parseFloat(e["Production (mmscf)"]) }})
                
                this.ResultsData.PzData = pz_plot_data
                this.ResultsData.FeData = fe_plot_data
                this.PzRegression = bod.linear_model_p
                this.PzOGIP = Math.round(-this.PzRegression[1] / this.PzRegression[0] / 1000)
                
                this.FeRegression = bod.linear_model_f
                this.FeOGIP =  Math.round(this.FeRegression[0]/ 1000)

                var pz_regress_plot = bod.data_set.map( (e) => {return {x: parseFloat(e["Production (mmscf)"]), y: parseFloat(e["Production (mmscf)"]) * this.PzRegression[0] + this.PzRegression[1] }})
                var fe_regress_plot = bod.data_set.map( (e) => {return {x: e["Eg"], y: e["Eg"] * this.FeRegression[0] }})
                this.ResultsData.PzLine = pz_regress_plot
                this.ResultsData.FeLine = fe_regress_plot
            
        },
      }
    }
</script>