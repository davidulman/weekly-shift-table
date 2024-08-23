<script setup>
import { ref, computed } from 'vue';
import html2canvas from 'html2canvas';

const headers = ['יום', 'ערב', 'לילה'];
const contentToCapture = ref(null);

const getNextMonday = () => {
  const date = new Date();
  const day = date.getDay();
  const diff = day <= 1 ? 1 - day : 8 - day;
  date.setDate(date.getDate() + diff);
  return date;
};

const formatDate = (date) => {
  return date.toLocaleDateString('he-IL', {
    year: 'numeric',
    month: 'numeric',
    day: 'numeric',
  });
};

const startDate = getNextMonday();
const endDate = new Date(startDate);
endDate.setDate(endDate.getDate() + 4);

const dateRange = computed(() => {
  return `${formatDate(startDate)} - ${formatDate(endDate)}`;
});

const hebrewDays = ['ראשון', 'שני', 'שלישי', 'רביעי', 'חמישי', 'שישי', 'שבת'];

const rows = ref([]);

for (let i = 0; i < 5; i++) {
  const currentDate = new Date(startDate);
  currentDate.setDate(currentDate.getDate() + i);
  rows.value.push({
    day: `${hebrewDays[currentDate.getDay()]} - ${formatDate(currentDate)}`,
    evening: '',
    night: '',
  });
}

const captureScreenshot = () => {
  if (contentToCapture.value) {
    html2canvas(contentToCapture.value).then((canvas) => {
      const link = document.createElement('a');
      link.download = 'shift-schedule-screenshot.png';
      link.href = canvas.toDataURL();
      link.click();
    });
  }
};
</script>
<template>
  <div class="container">
    <div class="content" ref="contentToCapture">
      <h1 class="title">לוח משמרות שבועי</h1>
      <div class="date-range">
        <p>{{ dateRange }}</p>
      </div>
      <div class="remarks">
        <p>* משמרת אחה"צ: 16:00-23:00</p>
        <p>* משמרת לילה: 23:00-03:30</p>
        <p>
          * מי שעושה משמרות לילה מחויב לבדוק את כל המיילים במידה ויש משהו דחוף
          בבקשה לטפל בלקוח.
        </p>
      </div>

      <div class="table-container">
        <table>
          <thead>
            <tr>
              <th v-for="header in headers" :key="header">{{ header }}</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(row, index) in rows" :key="index">
              <td>{{ row.day }}</td>
              <td><input v-model="row.evening" /></td>
              <td><input v-model="row.night" /></td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
    <button @click="captureScreenshot" class="screenshot-btn">צלם מסך</button>
  </div>
</template>

<style scoped>
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
  direction: rtl;
  text-align: right;
  background-color: #f8f9fa;
  color: #333;
}

.content {
  width: 100%;
}

.title {
  color: #2c3e50;
  text-align: center;
  margin-bottom: 20px;
}

.remarks {
  background-color: #e9ecef;
  border-radius: 8px;
  padding: 15px;
  margin-bottom: 20px;
  font-size: 0.9em;
  line-height: 1.5;
}

.date-range {
  text-align: center;
  font-size: 1.2em;
  margin-bottom: 20px;
  color: #3498db;
}

.table-container {
  background-color: #ffffff;
  border-radius: 8px;
  padding: 20px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

table {
  width: 100%;
  border-collapse: collapse;
}

th,
td {
  padding: 12px;
  border: 1px solid #dee2e6;
}

th {
  background-color: #e9ecef;
  color: #495057;
}

input {
  width: 100%;
  padding: 8px;
  border: 1px solid #ced4da;
  border-radius: 4px;
  transition: border-color 0.15s ease-in-out, box-shadow 0.15s ease-in-out;
}

input:focus {
  border-color: #80bdff;
  outline: 0;
  box-shadow: 0 0 0 0.2rem rgba(0, 123, 255, 0.25);
}

.screenshot-btn {
  margin-top: 20px;
  padding: 10px 20px;
  background-color: #3498db;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 1em;
  transition: background-color 0.3s ease;
}

.screenshot-btn:hover {
  background-color: #2980b9;
}
</style>
