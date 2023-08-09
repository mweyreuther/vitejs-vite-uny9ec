<template>
  <div class="fixed inset-10 flex flex-col bg-white rounded-lg shadow-lg p-5">
    <div>{{ positions }}</div>
    <div class="relative bg-green-300 grow">
      <div
        class="
          absolute
          bg-red-500/30
          truncate
          border border-gray-800
          rounded-full
          px-2
        "
        v-for="(appointment, i) in sortedAppointments"
        :key="JSON.stringify(appointment)"
        :style="positions[i]"
      >
        appointment {{ i + 1 }}
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { ref, computed } from 'vue';

const startCalendar = ref(10);
const endCalendar = ref(18);
const delta = computed(() => endCalendar.value - startCalendar.value);

const appointments = ref([
  [10, 11],
  [12, 13],
  [12, 14],
  [13, 15],
  [14, 15],
  [16, 18],
]);

const sortedAppointments = computed(() => {
  return appointments.value.sort((a, b) => {
    return a[0] - b[0];
  });
});

const positions = computed(() => {
  return sortedAppointments.value.map(([start, end], i, arr) => {
    const left = ((start - startCalendar.value) / delta.value) * 100;
    const right = ((endCalendar.value - end) / delta.value) * 100;

    return { left: `${left}%`, right: `${right}%` };
  });
  /*  .reduce(function (total, value, i) {
      const arr = sortedAppointments.value;
      const collision = hasCollision(arr[i], arr[i - 1]);
      const row = collision ? total[i - 1].row + 1 : total[i - 1]?.row ?? 0;
      const top = `${row * 40}px`;

      total.push({ ...value, top, collision, row });
      return total;
    }, []);*/
});

function hasCollision(index, compareIndex, arr) {
  const current = arr[index];
  const compare = arr[compareIndex];
  const start = current?.[0];
  const compareEnd = compare?.[1];
  return compareEnd - start > 0;
}

function determineRow(index, arr) {
  if (index === 0) return 0;
  if (hasCollision(index, 0, arr)) {
    if (index === 1) return 1;
    return arr[i - 1].row + 1;
  }
  return 0;
}
</script>
