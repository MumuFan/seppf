<template>
  <div id="founderCharts" style="width: 99%; height: 300px;"></div>
</template>
<script>
let echarts = require("echarts/lib/echarts");
require("echarts/lib/chart/bar");
require("echarts/lib/component/tooltip");
require("echarts/lib/component/toolbox");
require("echarts/lib/component/title");
require("echarts/lib/component/legend");
import sepp from "@/assets/theme/charts/sepp"; 
export default {
  data: function() {
    return {
      chartsOptions: ""
    };
  },
  props: ["dfounders"],

  created() {
    let _self =  this;
    let dfounders = _self.sortData(_self.dfounders, "founder", "children");
    let usersData = [];
    let legendData = [];
    let seriesData = [];

    dfounders.forEach(data => {
      usersData.push(data.founder);
    });

    eval(localStorage.getItem("defectInfluence")).forEach(item => {
      let numsData = [];
      dfounders.forEach(data => {
        let numValue = 0;
        data.children.forEach(child => {
          if (child.influence === item.influenceId) {
            numValue = child.num;
          }
        });
        numsData.push(numValue);
      });

      legendData.push(item.influenceName);
      seriesData.push({
        name: item.influenceName,
        type: "bar",
        barMaxWidth: '50',
        stack: "数量",
        data: numsData
      });
    });

    _self.chartsOptions = {
      title: {
        text: "缺陷提交人分布",
        top: "5px",
        left: "10px"
      },
      tooltip: {
        trigger: "axis",
        axisPointer: { type: "shadow" }
      },
      toolbox: {
        right: 0,
        top: 0,
        show: true,
        feature: {
          dataView: { show: true, readOnly: false },
          restore: { show: true },
          saveAsImage: { show: true }
        },
        padding: 10
      },
      legend: {
        data: legendData
      },
      grid: {
        top: 60,
        left: 50,
        right: 40,
        bottom: 40,
        show: true
      },
      xAxis: {
        type: "category",
        data: usersData
      },
      yAxis: {
        type: "value",
        axisLabel: {
          formatter: "{value}"
        }
      },
      series: seriesData
    };
  },

  mounted() {
    let _self =  this;
    let charts = document.getElementById("founderCharts");

    let dataCharts = echarts.init(charts, sepp);
    dataCharts.setOption(_self.chartsOptions);
  },

   methods: {
    sortData(json, idKey, childKey) {
      let temp = [];
      let result = [];
      for (let i = 0; i < json.length; i++) {
        temp.push(json[i][idKey]);
      }
      temp = temp.filter(function(element, index, array) {
        return array.indexOf(element) === index;
      });

      for (let k = 0; k < temp.length; k++) {
        let children = json.filter(function(d) {
          return d[idKey] === temp[k];
        });
        result.push({
          [idKey]: temp[k],
          [childKey]: children
        });
      }
      return result;
    },
   }
};
</script>
