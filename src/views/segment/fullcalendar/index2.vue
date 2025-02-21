<template>
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

<script>
export default {
  data() {
    return {
      currentYear: new Date().getFullYear(),
      currentMonth: new Date().getMonth(),
      weekDays: ["日", "一", "二", "三", "四", "五", "六"],
      events: [
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
      ],
      days: [],
      selectedEvent: null,
    };
  },
  mounted() {
    this.generateCalendar();
  },
  methods: {
    generateCalendar() {
      let firstDay = new Date(this.currentYear, this.currentMonth, 1).getDay();
      let totalDays = new Date(this.currentYear, this.currentMonth + 1, 0).getDate();
      let daysArray = Array(firstDay).fill({ date: "", fullDate: "", events: [] });
      for (let i = 1; i <= totalDays; i++) {
        let dateStr =
          `${this.currentYear}-` +
          String(this.currentMonth + 1).padStart(2, "0") +
          `-${String(i).padStart(2, "0")}`;
        let dayEvents = this.events.filter((e) => dateStr >= e.start && dateStr <= e.end);
        daysArray.push({ date: i, fullDate: dateStr, events: dayEvents });
      }
      this.days = daysArray;
    },
    prevMonth() {
      if (this.currentMonth === 0) {
        this.currentYear--;
        this.currentMonth = 11;
      } else {
        this.currentMonth--;
      }
      this.generateCalendar();
    },
    nextMonth() {
      if (this.currentMonth === 11) {
        this.currentYear++;
        this.currentMonth = 0;
      } else {
        this.currentMonth++;
      }
      this.generateCalendar();
    },
    showDetails(day) {
      if (day.events.length) {
        this.selectedEvent = day.events[0];
      }
    },
    getEventStyle(event, date, index) {
      const colors = {
        maintenance: "#f39c12",
        repair: "#3498db",
        installation: "#9b59b6",
        upgrade: "#2ecc71",
      };
      let start = new Date(event.start).getDate();
      let end = new Date(event.end).getDate();
      let current = new Date(date).getDate();
      return {
        backgroundColor: colors[event.type] || "#ccc",
        width: "100%",
        position: "absolute",
        left: 0,
        // top: `${30 + index * 22}px`,
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
    },
  },
};
</script>

<style>
.calendar-container {
  width: 90%;
  margin: auto;
}
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
</style>
