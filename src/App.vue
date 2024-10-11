<template>
  <div class="calender-container">
    <el-card>
      <!-- 自定义头部，切换视图类型和切换日期 -->
      <div class="calender-header mb2">
        <div class="header-left">
          <span class="time-title">{{ currentDate }}</span>
          <el-button
            :icon="ArrowLeftBold"
            circle
            @click="
              Tcalendar.prev();
              dayTime();
            "
          />
          <el-button
            :icon="ArrowRightBold"
            circle
            @click="
              Tcalendar.next();
              dayTime();
            "
          />
        </div>
        <div class="header-right">
          <el-button
            class="btn-m2"
            type="primary"
            @click="
              Tcalendar.today();
              dayTime();
            "
            plain
            round
            >今天</el-button
          >
          <el-select
            v-model="type"
            placeholder="视图类型"
            style="width: 80px"
            @change="changeType"
          >
            <el-option label="月" value="dayGridMonth" />
            <el-option label="周" value="timeGridWeek" />
            <el-option label="天" value="timeGridDay" />
            <el-option label="列" value="listWeek" />
          </el-select>
          <!-- 选择月份的日期框 -->
          <el-date-picker
            v-if="type === 'dayGridMonth'"
            v-model="showMonth"
            type="month"
            :clearable="false"
            placeholder="请选择日期"
            style="margin-left: 10px; vertical-align: middle"
            @change="changeDate"
          />
          <el-button class="ml2" type="primary" :icon="Plus" plain
            >新增排班</el-button
          >
        </div>
      </div>
      <div ref="fullcalendar" class="card"></div>
    </el-card>

    <!-- 查看任务详情弹窗 -->
    <calendarDetailDialog
      v-model:dialogVisible="calendarDialogVisible"
      :detailInfo="detailInfo"
    />
    <!-- 新增任务弹窗 -->
    <drawerAddPlan v-model:drawerVisile="drawerVisile" />
  </div>
</template>

<script setup lang="ts">
import { ref, nextTick, onMounted } from "vue";
import { Calendar } from "@fullcalendar/core";
import dayGridPlugin from "@fullcalendar/daygrid";
import timeGridPlugin from "@fullcalendar/timegrid";
import listPlugin from "@fullcalendar/list";
import interactionPlugin from "@fullcalendar/interaction";
import dayjs from "dayjs";
import { ArrowLeftBold, ArrowRightBold, Plus } from "@element-plus/icons-vue";
import calendarDetailDialog from "./components/calendarDetailDialog.vue";
import drawerAddPlan from "./components/drawerAddPlan.vue";

const state = {
  infoList: [
    {
      id: "1",
      title: "老化实验",
      name: "张三",
      start: "2024-10-08",
      end: "2024-10-08",
      class: "tag_1",
      job: "产线员工",
      description: "XXXXXXXXX实验XXXXXXXXXXXXXXXXXXXXXXXXXXXX",
    },
    {
      id: "2",
      title: "第一个任务12312312312312312",
      name: "李四",
      start: "2024-10-10 13:30:00",
      end: "2024-10-10 14:30:00",
      class: "tag_2",
      job: "负责人",
      description: "测试XXXXXXX",
    },
    {
      id: "3",
      title: "第一个任务12312312312312312",
      name: "员工1",
      start: "2024-10-11 08:00:00",
      end: "2024-10-11 12:30:00",
      class: "tag_2",
      job: "员工",
      description: "测试XXXXXXXeqee",
    },
    {
      id: "4",
      title: "第一个任务12312312312312312",
      name: "员工",
      start: "2024-10-09 09:30:00",
      end: "2024-10-09 11:00:00",
      class: "tag_3",
      job: "生产员工3",
      description: "测试XXXXXXXeqee",
    },
    {
      id: "5",
      title: "第一个任务12312312312312312",
      name: "员工3",
      start: "2024-10-09 16:00:00",
      end: "2024-10-09 18:30:00",
      class: "tag_1",
      job: "生产员工2",
      description: "测试XXXXXXXeqee",
    },
    {
      id: "6",
      title: "第一个任务12312312312312312",
      name: "员工4",
      start: "2024-10-09 13:30:00",
      end: "2024-10-09 14:00:00",
      class: "tag_2",
      job: "生产员工1",
      description: "测试XXXXXXXeqee",
    },
  ],
  Tcalendar: ref(),
};
const fullcalendar = ref();
const Tcalendar = ref();
const type = ref("dayGridMonth"); // 默认月视图
const currentDate = ref(); // 当前时间
const showMonth = ref(dayjs().format("YYYY-MM")); // 默认当前月份
const calendarDialogVisible = ref(false); // 是否显示详情弹窗
const detailInfo = ref(); // 详情数据
const drawerVisile = ref(false); // 是否显示新增排班的抽屉

const initCalendar = () => {
  Tcalendar.value = new Calendar(fullcalendar.value, {
    plugins: [dayGridPlugin, timeGridPlugin, listPlugin, interactionPlugin],
    initialView: type.value,
    aspectRatio: 2.2, // 宽度比
    locale: "zh-cn",
    handleWindowResize: true,
    //   loading: loading //控制表格加载
    editable: true, // 允许编辑表格
    droppable: true, //允许从外部拖拽进入日历
    eventDurationEditable: true, //控制时间段是否可以拖动
    eventResizableFromStart: true, //控制事件是否可以拖动
    selectable: true, // 允许用户通过单击和拖动来突出显示多个日期或时间段
    firstDay: 1, // 设置一周中显示的第一天是哪天，周日是0，周一是1，类推。
    unselectAuto: true, // 当点击页面日历以外的位置时，是否自动取消当前的选中状态
    dayMaxEvents: true, //在dayGrid视图中，给定日期内的最大事件数
    headerToolbar: false, // 关闭默认日历头部，采取自定义的方式切换日历视图
    // allDaySlot: false, // 关闭全天选项
    allDayText: "全天",
    nowIndicator: true,
    eventMaxStack: 2,
    events: state.infoList, //主要数据
    eventClassNames: function (arg) {
      // 添加自定义class
      return [arg.event.extendedProps.class];
    },
    eventContent: function (arg) {
      // 日历上event显示的样式
      const italicEl = document.createElement("div");
      // 列表才显示
      if (type.value === "listWeek") {
        // 标题
        const nameEl = document.createElement("h4");
        nameEl.setAttribute("class", `h4`);
        nameEl.innerHTML = arg.event.extendedProps.name;
        italicEl.append(nameEl);
        // 岗位
        const text1El = document.createElement("p");
        text1El.innerHTML = arg.event.extendedProps.job;
        italicEl.append(text1El);
        // 面试官
        const text2El = document.createElement("p");
        text2El.innerHTML = "描述：" + arg.event.extendedProps.job;
        italicEl.append(text2El);
      } else {
        // 标题
        const titleEl = document.createElement("div");
        titleEl.setAttribute("class", `calendar-title`);
        const nameEl = document.createElement("span");
        nameEl.innerHTML = arg.event.extendedProps.name;
        titleEl.append(nameEl);
        // 时间
        const timeEl = document.createElement("span");
        if (arg.event.start && arg.event.end) {
          timeEl.innerHTML =
            dayjs(arg.event.start).format("HH:mm") +
            "-" +
            dayjs(arg.event.end).format("HH:mm");
          if (timeEl.innerHTML !== "00:00-00:00") {
            titleEl.append(timeEl);
          }
        }
        italicEl.append(titleEl);
      }
      italicEl.setAttribute("class", `calendar-card`);
      return { domNodes: [italicEl] };
    },
    noEventsContent: function () {
      const noEl = document.createElement("div");
      noEl.innerHTML = "暂无日程安排，请安排相关日程";
      return { domNodes: [noEl] };
    },
    // 点击查看时触发
    eventClick: function (info) {
      handleClick(info);
    },
    // 视图选择日期触发
    select: function (info) {
      handleSelectDate(info);
    },
    // 拖拽event大小时触发
    eventResize: function (info) {
      handleEventResize(info);
    },
    // 拖拽停止时触发
    eventDrop: function (info) {
      handleDrap(info);
    },
  });
  Tcalendar.value.render();
};

//   切换类型
const changeType = (type: any) => {
  Tcalendar.value.changeView(type);
  dayTime();
};

/**
 * 获取当前时间
 */
const dayTime = () => {
  if (type.value === "dayGridMonth") {
    currentDate.value = dayjs(Tcalendar.value.getDate()).format("YYYY年MM月");
    // showMonth.value = dayjs(Tcalendar.value.getDate()).format('YYYY-MM');
  } else if (type.value === "timeGridWeek" || type.value === "listWeek") {
    currentDate.value =
      dayjs(Tcalendar.value.getDate()).format("YYYY年MM月DD日") +
      " - " +
      dayjs(Tcalendar.value.getDate()).add(6, "day").format("DD日");
  } else if (type.value === "timeGridDay") {
    currentDate.value = dayjs(Tcalendar.value.getDate()).format(
      "YYYY年MM月DD日"
    );
  }
};

/**
 * 修改月份
 * @param date 跳转日期
 */
const changeDate = (date: any) => {
  Tcalendar.value.gotoDate(dayjs(date).format("YYYY-MM"));
  currentDate.value = dayjs(date).format("YYYY年MM月");
};

/**
 * 拖拽调整大小
 */
const handleDrap = (info: any) => {
  console.log("info1--", info);
};
/**
 * 拖拽调整大小时触发
 */
const handleEventResize = (info: any) => {
  console.log("info2--", info);
};

/**
 * 点击事件，查看任务详情
 */
const handleClick = (info: any) => {
  console.log("info-3-", info.event);
  detailInfo.value = info.event;
  calendarDialogVisible.value = true;
};

const handleSelectDate = (info: any) => {
  console.log("info4--", info);
  drawerVisile.value = true;
};
const handleOk = () => {};
onMounted(() => {
  nextTick(() => {
    initCalendar();
    dayTime();
  });
});
</script>

<style lang="scss" scoped>
.calender-container {
  width: 1525px;
  position: relative;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
.calender-header {
  display: flex;
  justify-content: space-between;
  .header-left,
  .header-right {
    display: flex;
    align-items: center;
    .time-title {
      font-weight: bold;
      margin-right: 20px;
    }
  }
  .header-left {
    h1 {
      margin-right: 20px;
    }
  }
}

.btn-m2 {
  margin-right: 20px;
}
.ml2 {
  margin-left: 20px !important;
}
.mb2 {
  margin-bottom: 20px !important;
}
</style>
<style lang="scss">
.fc-col-header {
  background-color: #fafafa;
}
.calendar-card {
  display: block;
  padding: 2px 4px;
}
.calendar-title {
  display: flex;
  align-items: center;
  margin-left: 10px;
  font-size: 13px;
  span {
    margin-right: 5px;
  }
}
.fc .fc-daygrid-day.fc-day-today {
  background-color: rgba(101, 180, 230, 0.2);
  .fc-daygrid-day-number {
    background-color: #00b578;
    color: #fff;
    border-radius: 4px;
  }
}
.fc .fc-highlight {
  background: rgba(101, 180, 230, 0.2);
}
.fc .fc-timegrid-col.fc-day-today {
  background-color: rgba(101, 180, 230, 0.2);
}

.tag_1,
.tag_2,
.tag_3 {
  border-radius: 20px;
}
.tag_1 {
  background-color: #fab6b6;
}
.tag_2 {
  background-color: #a0cfff;
}
.tag_3 {
  background-color: #c8c9cc;
}
</style>
