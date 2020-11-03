<template>
  <div class='demo-app'>
    <div class='demo-app-main'>
      <FullCalendar
        class='demo-app-calendar'
        :options='calendarOptions'
      >
        <template v-slot:eventContent='arg'>
          <b>{{ arg.timeText }}</b>
          <i>{{ arg.event.title }}</i>
        </template>
      </FullCalendar>
    </div>
  </div>
</template>

<script>
import FullCalendar from '@fullcalendar/vue'
import dayGridPlugin from '@fullcalendar/daygrid'
import timeGridPlugin from '@fullcalendar/timegrid'
import interactionPlugin from '@fullcalendar/interaction'
import { createTimeUnitId,timeUnitIdToTime } from '@/utils/calendar-utils'

export default {
  name: 'Calendar',
  components: {
    FullCalendar // make the <FullCalendar> tag available
  },
  data: function() {
    return {
      calendarOptions: {
        /***************************
        *    日历的默认配置
        *    一般不作修改
        ***************************/ 
        plugins: [
          dayGridPlugin,
          timeGridPlugin,
          interactionPlugin // needed for dateClick
        ],
        headerToolbar: {
          left: 'prev',
          center: 'title',
          right: 'next'
        },
        // 默认展示视图：周
        initialView: 'timeGridWeek',
        // 是否使用选择的占位符
        selectMirror: true,
        // 选择事件的时间间隔可调整
        eventDurationEditable: true,
        // 改成中文日历
        locale: "zh-cn",
        // 全天选项
        allDaySlot: false,
        // 日历的宽高比例：宽：高
        aspectRatio: 3,
        // 日历内容的高度
        contentHeight: "auto",
        // 不允许用户选择已经选择过的时间点
        selectOverlap: false,

        /***************************
        *     用户可调整的属性
        *  与调整日历格式相关功能有关
        ***************************/ 
        // 是否显示周末
        weekends: true,
        // 设置默认显示的时间间隔
        slotDuration: '01:00', // 1 hours
        // 选择的时间的默认间隔（应与slotDuration保持一致）
        defaultTimedEventDuration: '01:00',
        // 日历显示的最早时间
        slotMinTime: "06:00:00",
        // 日历显示的最晚时间
        slotMaxTime: "22:00:00",
        // 强调日历中的某些时间段
        businessHours: {
            daysOfWeek: [ 1, 2, 3, 4, 5], // 0是星期天，1-6周一到周六
            startTime: '08:00', // 高亮开始时间
            endTime: '20:00', // 高亮结束时间
        },
        // 隐藏一周当中的某天
        hiddenDays: [  ], // 隐藏周二

        /***************************
        *     开发者根据页面功能
        *      需要调整的属性
        ***************************/ 
        // 日历是否可选
        selectable: true,
        // 背景时间：用于显示发起者未选择的时间
        events: [
          {
            // 传入的数据应该是发起者未选择的时间
            groupId: "hostChoose",
            start: '2020-11-01T10:00:00',
            end: '2020-11-01T16:00:00',
            // display: 'background',
            // backgroundColor: 'red'
            backgroundColor: '#FF6633',
            
        title: '1'
          }
        ],

        /***************************
        *         回调函数
        ***************************/ 
        select: this.handleDateSelect,
        eventClick: this.handleEventClick,
        eventsSet: this.handleEvents,
        slotLaneContent: this.handleLaneContent,
        // eventContent: this.handleEventContent
      },
      // 存储选择的时间
      selectTime: [],
    }
  },
  methods: {
    // 是否显示周末
    handleWeekendsToggle() {
      this.calendarOptions.weekends = !this.calendarOptions.weekends // update a property
    },
    // 创建已经选择的时间
    handleDateSelect(selectInfo) {
      let calendarApi = selectInfo.view.calendar
      calendarApi.unselect() // clear date selection
      calendarApi.addEvent({
        // 此处的id就是对应timeUnit的id
        id: createTimeUnitId(selectInfo.start),
        start: selectInfo.start,
        groupId: 'hostSelect',
      })
    },
    // 点击已选择的时间，删除已经选择的时间
    handleEventClick(clickInfo) {
      // 如果是创建事件页面,点击就是取消选择
      // clickInfo.event.remove();

      // 如果是填写事件页面
      // 先判断当前时间是 inviteeSelect 或者 hostSelect
      if(clickInfo.event.groupId === 'hostSelect'){
        clickInfo.event.remove();
        clickInfo.view.calendar.addEvent({
          id: clickInfo.event.id,
          start: clickInfo.event.start,
          groupId: 'inviteeSelect',
          backgroundColor: '#003399',
          borderColor: '#003399',
          title: '2'
        })
      }else{
        clickInfo.event.remove();
        clickInfo.view.calendar.addEvent({
          id: clickInfo.event.id,
          start: clickInfo.event.start,
          groupId: 'hostSelect',
          backgroundColor: '#3788d8',
          borderColor: '#3788d8',
          title: '1'
        })
      }


      // 若是结果页面，返回点击时间的id，根据id查找数据，渲染
      // return clickInfo.event.id;
    },
    // 更新存储的已经选择的时间
    handleEvents(events) {
      this.selectTime = events;
    },
    // 在日历的单元格内添加时间标注
    handleLaneContent(arg) {
      // 用于存储新建的元素
      let arrayOfDomNodes = [];
      for (let index = 0; index < 7 - this.calendarOptions.hiddenDays.length; index++) {
        let timeEl = document.createElement('span');
        // 为新建的元素添加属性
        timeEl.classList.add("time-unit");
        timeEl.innerHTML = arg.date.toString().slice(16,21);
        arrayOfDomNodes.push(timeEl);
      }
      return { domNodes: arrayOfDomNodes };
    },
    // 用于结果页面展示事件缺席人数
    // handleEventContent(arg){
    //   let timeEl = document.createElement('b');
    //   let absentEl = document.createElement('span');

    //   timeEl.innerHTML = arg.event.start.toString().slice(16,21);

    //   absentEl.innerHTML = '3';

    //   let arrayOfDomNodes = [];
    //   arrayOfDomNodes.push(timeEl);
    //   arrayOfDomNodes.push(absentEl);
      
    //   return { domNodes: arrayOfDomNodes }
    // }
  }
}
</script>

<style lang='css'>
h2 {
  margin: 0;
  font-size: 16px;
}
ul {
  margin: 0;
  padding: 0 0 0 1.5em;
}
li {
  margin: 1.5em 0;
  padding: 0;
}
b { /* used for event dates/times */
  margin-right: 3px;
}
.demo-app {
  display: flex;
  min-height: 100%;
  font-family: Arial, Helvetica Neue, Helvetica, sans-serif;
  font-size: 14px;
}
.demo-app-main {
  flex-grow: 1;
  padding: 3em;
}
.fc { /* the calendar root */
  max-width: 1100px;
  margin: 0 auto;
}

.fc-timegrid-slot-lane {
  text-align: center;
  display: flex;
}

.fc-timegrid-slot-lane span {
  flex: 1;
  /* background-color: cornflowerblue; */
}

.fc-event-main span i{
  float: right;
  /* right: 20px; */
  margin-right: 10px;
  color: #CC0000;
  font-weight: bold;
}
</style>

TODO: 
1.与会者选择的时间颜色的 选定 渲染
2.结果页面 最佳时间和推荐时间 颜色的选定
3.结果页面 不能参与人数的渲染 颜色