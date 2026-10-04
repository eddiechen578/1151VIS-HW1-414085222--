<script setup>
import * as d3 from "d3";
import { onMounted, ref } from "vue";

const chart = ref(null);

const data = [
  { month: "January", sales: 120 },
  { month: "February", sales: 180 },
  { month: "March", sales: 150 },
  { month: "April", sales: 220 },
  { month: "May", sales: 190 },
  { month: "June", sales: 250 }
];

onMounted(() => {

  const width = 800;
  const height = 500;

  const margin = {
    top: 60,
    right: 30,
    bottom: 80,
    left: 70
  };

  // Create SVG
  const svg = d3
      .select(chart.value)
      .append("svg")
      .attr("width", width)
      .attr("height", height);

  // X Scale
  const x = d3
      .scaleBand()
      .domain(data.map(d => d.month))
      .range([
        margin.left,
        width - margin.right
      ])
      .padding(0.2);

  // Y Scale
  const y = d3
      .scaleLinear()
      .domain([0, d3.max(data, d => d.sales)])
      .nice()
      .range([
        height - margin.bottom,
        margin.top
      ]);

  // X Axis
  svg
      .append("g")
      .attr(
          "transform",
          `translate(0, ${height - margin.bottom})`
      )
      .call(d3.axisBottom(x));

  // Y Axis
  svg
      .append("g")
      .attr(
          "transform",
          `translate(${margin.left}, 0)`
      )
      .call(d3.axisLeft(y));

  // Bars
  svg
      .selectAll(".bar")
      .data(data)
      .enter()
      .append("rect")
      .attr("class", "bar")
      .attr("x", d => x(d.month))
      .attr("y", d => y(d.sales))
      .attr("width", x.bandwidth())
      .attr(
          "height",
          d =>
              height -
              margin.bottom -
              y(d.sales)
      );

  // Values
  svg
      .selectAll(".value")
      .data(data)
      .enter()
      .append("text")
      .attr("class", "value")
      .attr(
          "x",
          d => x(d.month) + x.bandwidth() / 2
      )
      .attr(
          "y",
          d => y(d.sales) - 8
      )
      .attr("text-anchor", "middle")
      .text(d => d.sales);

  // Title
  svg
      .append("text")
      .attr("x", width / 2)
      .attr("y", 30)
      .attr("text-anchor", "middle")
      .attr("class", "title")
      .text("Monthly Sales");
});
</script>

<template>
  <div ref="chart" class="chart"></div>
</template>

<style scoped>
.chart {
  display: flex;
  justify-content: center;
  margin-top: 30px;
}

.bar {
  fill: steelblue;
}

.bar:hover {
  fill: orange;
}

.title {
  font-size: 20px;
  font-weight: bold;
}
</style>