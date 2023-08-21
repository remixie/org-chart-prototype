<script setup lang="ts">
import * as d3 from 'd3';
import { OrgChart } from 'd3-org-chart';
import json from '../assets/members.json';
import { ref, watch, onMounted } from 'vue';
const renderChart = function (data) {
  if (!chartReference.value) {
    chartReference.value = new OrgChart();
  }
  chartReference.value
    .container(svgElementContainer.value) // node or css selector
    .data(data)
    .nodeHeight((d) => 120)
    .onNodeClick((d) => console.log(d + ' node clicked'))
    .render();
};

const chartReference = ref(null);
const svgElementContainer = ref(null);

watch(chartReference, (value) => renderChart(value));

onMounted(async () => {
  const data = await d3.csv(
    'https://raw.githubusercontent.com/bumbeishvili/sample-data/main/org.csv'
  );
  //console.log(data);

  let chart = new OrgChart()
    .nodeContent(function (d, i, arr, state) {
      return `
      <div class='bg-gray-200 h-full rounded-xl pt-2'>
        <div class='bg-blue-600 text-center text-white text-sm font-bold rounded-md mx-2'>${d.data.name}</div>
        <div class='text-center uppercase mt-2 pb-2'>${d.data.positionName}</div>
        <div class='grid grid-cols-3'>
          <div class='col-span-1 bg-white rounded-full h-20 w-20 ml-2'>
          <img src='${d.data.imageUrl}' class='h-20 m-auto rounded-full'>
          </div>
          <div class='col-span-2 h-full items-center pl-4 flex'>
          <div>
            <div><b class='pr-1'>Grade:</b>${d.data.id}</div>
            <div><b class='pr-1'>Funding:</b>${d.data.office}</div>
            </div>
          </div>
        </div>
      </div>`;
    })
    .container('.chart-container')
    .data(data)
    .render();
});
</script>

<template>
  <div ref="svgElementContainer" class="chart-container"></div>
  <!--<div class="text-xs">{{ json }}</div>-->
</template>

<style scoped></style>
