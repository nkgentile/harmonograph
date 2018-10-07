<template>
  <svg
    :width="width"
    :height="height"
  >
    <g>
      <path
        :d="path"
        fill="none"
        stroke="red"
      />
    </g>
  </svg>
</template>

<script>
  import {
    extent,
  } from 'd3-array';

  import {
    scaleLinear,
  } from 'd3-scale';

  import {
    curveBasis,
    line,
  } from 'd3-shape';

  import {
    flatten,
    map,
    pipe,
    range,
    splitEvery,
    times,
  } from 'ramda';

  export default {
    props: {
      width: {
        type: Number,
        default: 640,
      },

      height: {
        type: Number,
        default: 640,
      },

      amplitudes: {
        type: Array,
        default: () => [
          10, 3, 2, 5
        ],
      },

      time: {
        type: Number,
        default: 0,
      },

      frequencies: {
        type: Array,
        default: () => [
          1, 4, 1, 10
        ],
      },

      phases: {
        type: Array,
        default: () => [
          1, 1, 1, 1
        ],
      },

      dampings: {
        type: Array,
        default: () => [
          1, 1, 1, 1
        ],
      },

      frames: {
        type: Array,
        default: () => range(0, 1000),
      },
    },

    computed: {
      scalePeriod(){
        const TWOPI = Math.PI * 2;

        return scaleLinear()
          .domain([0, 1000])
          .range([-TWOPI, TWOPI])
          .clamp(true);
      },

      extent(){
        return extent(flatten(this.points));
      },

      scalePosition(){
        return scaleLinear()
          .domain(this.extent)
          .range([0, this.width > this.height ? this.height : this.width ])
          .clamp(true);
      },

      points(){
        return pipe(
          map(this.scalePeriod),
          map(this.positionAtTime),
        )(this.frames);
      },

      path(){
        const scaledPoints = pipe(
          flatten,
          map(this.scalePosition),
          splitEvery(2),
        )(this.points);

        return line().curve(curveBasis)(scaledPoints);
      },
    },

    methods: {

      pendulumPositionAtTime(a, f, p, d, t){
        return a * Math.sin(t * f * p) * Math.exp(-1 * d * t);
      },

      positionAtTime(t){
        const a1 = this.amplitudes[0],
          a2 = this.amplitudes[1],
          a3 = this.amplitudes[2],
          a4 = this.amplitudes[3],

          f1 = this.frequencies[0],
          f2 = this.frequencies[1],
          f3 = this.frequencies[2],
          f4 = this.frequencies[3],

          p1 = this.phases[0],
          p2 = this.phases[1],
          p3 = this.phases[2],
          p4 = this.phases[3],

          d1 = this.dampings[0],
          d2 = this.dampings[1],
          d3 = this.dampings[2],
          d4 = this.dampings[3],

          pt = this.pendulumPositionAtTime,
        
          x = pt(a1, f1, p1, d1, t) + pt(a2, f2, p2, d2, t),
          y = pt(a3, f3, p3, d3, t) + pt(a4, f4, p4, d4, t);

          return [x, y];
      },

    },
  };
</script>

<style>
</style>
