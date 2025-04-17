<template>
  <div class="max-w-full mx-auto p-2">
    <table class="table-fixed w-full text-center text-xs md:text-sm mt-2 border-1">
      <thead>
        <tr>
          <th class="p-1 md:p-2 w-[40px]"></th>
          <th
            v-for="day in daysInWeek"
            :key="day"
            class="p-1 md:p-2 rounded-4xl"
            :class="{
              'bg-blue-700 text-white': selectedDate === dateMap[day],
              'bg-gray-300': !dateMap[day],
              'hover:bg-blue-100': dateMap[day],
            }"
          >
            {{ day.toUpperCase() }}
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="slot in ['AM', 'PM']" :key="slot">
          <td class="p-1 md:p-2 font-semibold">{{ slot }}</td>
          <td
            v-for="day in daysInWeek"
            :key="day + slot"
            class="p-1 md:p-2 rounded-4xl"
            :class="{
              'bg-blue-500 text-white': selectedSlot.date === dateMap[day] && selectedSlot.slot === slot,
              'bg-gray-100': !(selectedDate === dateMap[day]),
            }"
          >
            <span>
              {{ selectedDate === dateMap[day] && selectedSlot.slot === slot ? '●' : '休' }}
            </span>
          </td>
        </tr>
      </tbody>
    </table>

    <div class="mt-3 text-sm md:text-base" v-if="selectedSlot.date">
      <p class="text-green-700 font-medium">
        <strong>{{ selectedSlot.date }}</strong> - {{ selectedSlot.slot }}
      </p>
    </div>
  </div>
</template>

<script setup lang="ts">
// Dữ liệu tĩnh
const daysInWeek = ['月', '火', '水', '木', '金', '土', '日']

// Mapping ngày trong tuần sang ngày ISO cố định
const dateMap: Record<string, string> = {
  月: '2025-04-21',
  火: '2025-04-22',
  水: '2025-04-23',
  木: '2025-04-24',
  金: '2025-04-25',
  土: '',
  日: '',
}

// Ngày đang chọn cố định
const selectedDate = '2025-04-24'
const selectedSlot = {
  date: '2025-04-24',
  slot: 'AM',
}
</script>
