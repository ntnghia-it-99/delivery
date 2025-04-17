<template>
  <div class="max-w-3xl mx-auto bg-gray-100 min-h-screen flex flex-col">
    <header class="bg-gray-300 h-10 flex items-center justify-center">
      <h1 class="text-lg font-bold">ヘッダー</h1>
    </header>

    <div class="p-6 flex-1">
      <h1 class="text-xl font-bold mb-4">L1MON お届け可能日検索ツール</h1>
      <form class="space-y-4">
        <p>発着地を選択してください</p>

        <div class="flex gap-2 items-center">
          <div class="w-1/2">
            <label class="block font-medium text-xs text-center">薬局様の所在地</label>
            <Multiselect class="md:w-60 text-xs" v-model="pharmacyPostalCode" :options="postcode" placeholder="選択してください"
              :invalid="pharmacyPostalCode?.length === 0" />
          </div>

          <div class="flex items-center justify-center">
            <p class="text-lg">→</p>
          </div>

          <div class="w-1/2">
            <label class="block font-medium text-xs text-center">患者さま宅の所在地</label>
            <Multiselect class="md:w-60 text-xs" v-model="patientPostalCode" :options="postcode" placeholder="選択してください"
              :invalid="patientPostalCode?.length === 0" />
          </div>
        </div>


        <div>
          <label class="block font-medium">薬局様のお休みの曜日を選択してください</label>
          <calendar v-model:application-date="applicationDate" />
        </div>
        <div class="mt-4">
          <p>お申込日（変更不可）</p>
          <div class="mt-3 text-sm md:text-base w-35 h-10 bg-gray-200 rounded-lg p-2">
            <p class="text-black-700 font-medium">
              <strong>{{ new Date().toLocaleDateString('ja-JP') + '-' + currentTime }}</strong>
            </p>
          </div>

          <p v-if="isBefore14" class="text-red-500">
            ※14時00分以降のお申し込みはお申込み日が翌日以降になりますので、配送日も変更になります。必ず14:00までにお申込手続きをお願いします。
          </p>

          <p v-if="isAfter14" class="text-red-500">
            ※本日のお申込みは締め切りました。お申し込みは明日以降になりますが、必ず本日中にお申込手続きをお願いします。
          </p>
        </div>
        <button type="button" class="px-4 py-2 rounded-full hover:bg-blue-700 w-full border-1 h-12"
          @click="searchDates">
          お届け可能日を検索する
        </button>
      </form>
      <div v-if="deliveryDates.length" class="mt-6">
        <div class="flex justify-center my-2 mt-6">
          <div
            class="w-0 h-0 border-l-[40vw] border-l-transparent border-r-[40vw] border-r-transparent border-t-[40px] border-t-gray-500">
          </div>
        </div>
        <div class="bg-gray-300 mt-4 p-4">
          <h2 class="text-sm font-bold mb-2">お受け取り希望日・時間帯を選択してください。</h2>
          <div class="grid gap-3 grid-cols-[repeat(auto-fit,minmax(70px,1fr))] mb-7">
            <div class="bg-gray-200 text-center text-[10px] p-2 cursor-pointer rounded" :class="{
              'bg-red-500 text-white': idx === activeDateIndex
            }" v-for="(date, idx) in deliveryDates" :key="idx" @click="selectDate(idx)">
              {{ date.date }}
            </div>
          </div>

          <!-- Chọn thời gian giao -->
          <div class="grid gap-3 grid-cols-[repeat(auto-fit,minmax(70px,1fr))]" v-if="displayTime">
            <div class="bg-gray-200 text-center text-[10px] p-2 rounded-full cursor-pointer" :class="{
              'bg-yellow-500 text-white': idx === activeDateIndex2
            }" v-for="(time, idx) in timeSlots" :key="idx" @click="selectTime(idx)">
              {{ time }}
            </div>
          </div>

          <!-- Mũi tên -->
          <div class="flex justify-center my-2 mt-6" v-if="displayTimeDelivery">
            <div
              class="w-0 h-0 border-l-[40vw] border-l-transparent border-r-[40vw] border-r-transparent border-t-[40px] border-t-gray-500">
            </div>
          </div>
          <div class="bg-gray-200 mt-4 p-4" v-if="displayTimeDelivery">
            <div class="mb-4">
              <p>薬局への資材到着予定日</p>
              <button class="w-40 bg-gray-400 text-left">2025-04-16</button>
            </div>
            <div class="mb-4">
              <p>薬局からの集荷予定日</p>
              <button class="w-40 bg-gray-400 text-left">2025-04-16</button>
            </div>
          </div>
        </div>
      </div>

    </div>
    <footer class="bg-gray-300 h-10 flex items-center justify-center">
      <h1 class="text-lg font-bold">フッター</h1>
    </footer>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import Multiselect from 'vue-multiselect'
import 'vue-multiselect/dist/vue-multiselect.min.css'
import calendar from './calendar.vue'

// Dữ liệu cứng
const postcode = ref([
  '100-0001',
  '150-0001',
  '530-0001',
  '600-8001',
])

// Khởi tạo giá trị cho các input
const pharmacyPostalCode = ref('100-0001')
const patientPostalCode = ref('150-0001')

// Ngày đăng ký mặc định
const applicationDate = ref('2025-04-17')

// Mốc giờ hiện tại
const now = new Date()
const currentTime = `${now.getHours().toString().padStart(2, '0')}:${now.getMinutes().toString().padStart(2, '0')}`

// So sánh mốc 14h
const isBefore14 = computed(() => now.getHours() < 14)
const isAfter14 = computed(() => now.getHours() >= 14)

// Kết quả tìm được (giả lập cứng)
const deliveryDates = ref([
  { date: '2025-04-18' },
  { date: '2025-04-19' },
  { date: '2025-04-20' },
])
const searchDay = ref([
  { date: '2025-04-18' },
  { date: '2025-04-19' },
  { date: '2025-04-20' },
])
const activeDateIndex = ref(0)

const timeSlots = ref(['AM', 'PM'])
const activeDateIndex2 = ref(0)

const displayTime = ref(true)
const displayTimeDelivery = ref(true)

function searchDates() {
  // Logic xử lý khi nhấn nút tìm kiếm (nếu cần làm gì thêm)
  return searchDay
}

function selectDate(index: number) {
  activeDateIndex.value = index
  displayTime.value = true
}

function selectTime(index: number) {
  activeDateIndex2.value = index
  displayTimeDelivery.value = true
}
</script>
