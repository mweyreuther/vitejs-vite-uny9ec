<template>
  <div
    class="fixed inset-20 flex flex-col rounded-xl shadow-xl p-5 bg-gradient-to-b from-gray-700 to-gray-600"
  >
    <!-- <div>{{ positions }}</div> -->
    <div class="relative grow flex">
      <div
        class="even:bg-gray-900/50 odd:bg-gray-900/30 w-full grid place-items-end text-yellow-300 text-sm rounded-lg"
        v-for="i in delta"
      >
        <span class="-rotate-45 translate-x-1/2">
          {{ i + startCalendar }}:00h</span
        >
      </div>

      <div
        class="absolute bg-emerald-500/30 shadow-md shadow-emerald-500/20 truncate border-2 border-current rounded-lg px-2 py-5 text-center text-emerald-600"
        v-for="(interval, i) in sortedIntervals"
        :key="JSON.stringify(interval)"
        :style="positions[i]"
      >
        appointment {{ i + 1 }}
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { ref, computed } from "vue";

const startCalendar = ref(10);
const endCalendar = ref(18);
const delta = computed(() => endCalendar.value - startCalendar.value);

const intervals = ref([
  [10, 11],
  [10.5, 13],
  [10.6, 13.5],
  [12.5, 14],
  [14, 15],
  [14.5, 16],
  [16.25, 16.75],
  [17, 18],
]);

const sortedIntervals = computed(() => {
  return intervals.value.sort((a, b) => {
    return a[0] - b[0];
  });
});

const rows = computed(() => {
  return sortedIntervals.value.reduce((total, value, index, arr) => {
    if (index === 0) {
      value.push(0);
      return total;
    }

    for (let i = 0; i < index; i++) {
      const collision = hasCollision(index, i, arr);
      const prev = total[index - 1][2];
      if (collision) {
        value[2] = prev + 1;
      } else {
        value[2] = 0;
      }
    }

    return total;
  }, sortedIntervals.value);
});

const positions = computed(() => {
  return rows.value.map(([start, end, row], i, arr) => {
    const left = ((start - startCalendar.value) / delta.value) * 100;
    const right = ((endCalendar.value - end) / delta.value) * 100;
    const top = row * 4.5;
    return {
      left: `${left}%`,
      right: `${right}%`,
      top: `${top}rem`,
    };
  });
});

function hasCollision(startIndex: number, endIndex: number, arr: any) {
  const start = arr?.[startIndex]?.[0];
  const end = arr?.[endIndex]?.[1];
  // console.log(start, end, start - end);
  return start - end < 0;
}
</script>
