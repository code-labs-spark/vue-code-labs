<!-- 可以通过安装 FullCalendar 插件实现事件日历管理 -->
<!-- npm install @fullcalendar/core @fullcalendar/daygrid @fullcalendar/timegrid @fullcalendar/list @fullcalendar/interaction -->
<template>
  <SearchForm :updateTableList="updateTableList" :conditionList="conditionList" />
  <div class="calendar-container">
    <div class="calendar-header">
      <button @click="prevMonth">&lt;</button>
      <span>{{ currentYear }}年{{ currentMonth + 1 }}月</span>
      <button @click="nextMonth">&gt;</button>
    </div>
    <div class="calendar-grid">
      <div v-for="day in weekDays" :key="day" class="day-header">{{ day }}</div>
      <div v-for="(day, index) in days" :key="index" class="day-cell" @click="showDetails(day)">
        <span>{{ day.date }}</span>
        <div
          v-for="event in day.events"
          :key="event.id"
          class="event"
          :style="getEventStyle(event, day.fullDate, index)"
        >
          {{ event.title }}
        </div>
      </div>
    </div>
    <div v-if="selectedEvent" class="event-details">
      <h3>{{ selectedEvent.title }}</h3>
      <p>作业计划编号：{{ selectedEvent.id }}</p>
      <p>时间：{{ selectedEvent.start }} 至 {{ selectedEvent.end }}</p>
      <p>描述：{{ selectedEvent.description }}</p>
      <p>作业类型：{{ selectedEvent.type }}</p>
      <p>负责人：{{ selectedEvent.owner }}</p>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from "vue";
import SearchForm from "./components/SearchForm.vue";

const conditionList = ref([
  {
    id: 1,
    type: "input",
    label: "设备名称",
    prop: "patrolDevice",
    placeholder: "请输入",
    span: 4,
  },
  {
    id: 2,
    type: "select",
    label: "设备类型",
    prop: "deviceType",
    placeholder: "请选择",
    optionList: [
      {
        label: "光缆附挂",
        value: "1",
      },
      {
        label: "多站融合",
        value: "2",
      },
      {
        label: "共享杆塔",
        value: "3",
      },
    ],
    span: 4,
  },
  {
    id: 3,
    type: "select",
    label: "缺陷等级",
    prop: "defectPitfallLevel",
    placeholder: "请选择",
    optionList: [
      {
        label: "一般",
        value: "一般",
      },
      {
        label: "严重",
        value: "严重",
      },
      {
        label: "危急",
        value: "危急",
      },
    ],
    span: 4,
  },
  {
    id: 4,
    type: "input",
    label: "缺陷类型",
    prop: "defectPitfallType",
    placeholder: "请输入",
    span: 4,
  },
  {
    id: 5,
    type: "select",
    label: "发现方式",
    prop: "discoveryWay",
    placeholder: "请选择",
    optionList: [
      {
        label: "特殊巡视",
        value: "特殊巡视",
      },
      {
        label: "定期巡视",
        value: "定期巡视",
      },
    ],
    span: 4,
  },
  {
    id: 6,
    type: "input",
    label: "发现人",
    prop: "discoverer",
    placeholder: "请输入",
    span: 4,
  },
  {
    id: 7,
    type: "datetime",
    label: "发现日期",
    prop: "discoveryDate",
    span: 4,
  },
]);

const searchList = ref([]);

const currentYear = ref(new Date().getFullYear());
const currentMonth = ref(new Date().getMonth());
const weekDays = ["日", "一", "二", "三", "四", "五", "六"];
const selectedEvent = ref(null);

const events = ref([
  {
    id: 1,
    title: "A设备作业",
    start: "2024-10-01",
    end: "2024-10-03",
    type: "maintenance",
    description: "设备作业计划",
    owner: "张三",
  },
  {
    id: 2,
    title: "B设备作业",
    start: "2024-10-07",
    end: "2024-10-07",
    type: "repair",
    description: "B设备维护",
    owner: "李四",
  },
  {
    id: 3,
    title: "D设备",
    start: "2024-10-10",
    end: "2024-10-14",
    type: "installation",
    description: "D设备调试",
    owner: "王五",
  },
  {
    id: 4,
    title: "C设备作业",
    start: "2024-10-24",
    end: "2024-10-25",
    type: "upgrade",
    description: "C设备更换",
    owner: "赵六",
  },
]);

const days = ref([]);

const generateCalendar = () => {
  let firstDay = new Date(currentYear.value, currentMonth.value, 1).getDay();
  let totalDays = new Date(currentYear.value, currentMonth.value + 1, 0).getDate();
  let daysArray = Array(firstDay).fill({ date: "", fullDate: "", events: [] });

  for (let i = 1; i <= totalDays; i++) {
    let dateStr = `${currentYear.value}-${String(currentMonth.value + 1).padStart(2, "0")}-${String(i).padStart(2, "0")}`;
    let dayEvents = events.value.filter((e) => dateStr >= e.start && dateStr <= e.end);
    daysArray.push({ date: i, fullDate: dateStr, events: dayEvents });
  }
  days.value = daysArray;
};

const prevMonth = () => {
  if (currentMonth.value === 0) {
    currentYear.value--;
    currentMonth.value = 11;
  } else {
    currentMonth.value--;
  }
  generateCalendar();
};

const nextMonth = () => {
  if (currentMonth.value === 11) {
    currentYear.value++;
    currentMonth.value = 0;
  } else {
    currentMonth.value++;
  }
  generateCalendar();
};

const showDetails = (day) => {
  if (day.events.length) {
    selectedEvent.value = day.events[0];
  }
};

const getEventStyle = (event) => {
  const colors = {
    maintenance: "#f39c12",
    repair: "#3498db",
    installation: "#9b59b6",
    upgrade: "#2ecc71",
  };
  return {
    backgroundColor: colors[event.type] || "#ccc",
    width: "100%",
    position: "absolute",
    left: 0,
    height: "20px",
    lineHeight: "20px",
    fontSize: "12px",
    color: "white",
    textAlign: "center",
    whiteSpace: "nowrap",
    overflow: "hidden",
    textOverflow: "ellipsis",
    borderRadius: "4px",
  };
};

const updateTableList = (data) => {
  searchList.value = data;
};

onMounted(generateCalendar);
</script>

<style lang="scss" scoped>
.calendar-container {
  width: calc(100% - 20px);
  box-sizing: border-box;
  box-shadow: var(--el-box-shadow-light);
  background-color: #fff;
  border-radius: 4px;
  padding: 12px;
  margin: auto;
  .calendar-header {
    display: flex;
    justify-content: space-between;
    padding: 10px;
    font-size: 18px;
  }
  .calendar-grid {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 5px;
    text-align: center;
  }
  .day-header {
    font-weight: bold;
    padding: 10px;
  }
  .day-cell {
    border: 1px solid #ddd;
    padding: 10px;
    min-height: 80px;
    position: relative;
  }
  .event {
    width: 100%;
    position: absolute;
    left: 0;
    top: 30px;
    height: 20px;
    line-height: 20px;
    font-size: 12px;
    color: white;
    text-align: center;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
    border-radius: 4px;
    cursor: pointer;
  }
  .event-details {
    margin-top: 20px;
    padding: 10px;
    border: 1px solid #ddd;
    background: #f9f9f9;
  }
}
</style>
