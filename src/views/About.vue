<template>
  <div class="about">
    <Calendar @getTimeUnit='getTimeUnit' 
              @getTimeUnitId='getTimeUnitId'
              :Datas='datasToCalendar'/>
    <button @click="changeCalendarFormat">try</button>
  </div>
</template>

<script>
import Calendar from '@/components/FullCalendar'

export default {
  name: 'About',
  components: {
    Calendar
  },
  data() {
    return {
       /***************************
        *   传给日历组件的数据
        ***************************/ 
      datasToCalendar: {
        /***************************
        *   日历格式相关的数据
        ***************************/ 
        calendarFormat: {
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
              daysOfWeek: [ 1, 2, 3, 4, 5, 6, 0], // 0是星期天，1-6周一到周六
              startTime: '08:00', // 高亮开始时间
              endTime: '20:00', // 高亮结束时间
          },
          // 隐藏一周当中的某天
          hiddenDays: [  ], // 隐藏周二
          // 日历开始于结束的时间
          validRange: {
            start: '2020-10-11',
            end: '2020-11-11'
          },
        },
        /***************************
        *    与日历在各个页面
        *    的功能有关的数据
        ***************************/ 
        calendarFunction: {
          // 日历是否可选
          selectable: true,
          // 背景时间：用于显示发起者未选择的时间
          // 一开始是空，根据后端返回的数据进行初始化
          events: [
            {
              // 传入的数据应该是发起者未选择的时间
              groupId: "hostChoose",
              id: '2020-11-02-10:00:00-1',
              start: '2020-11-02T10:00:00',
              // end: '2020-11-01T16:00:00',
              // display: 'background',
              // backgroundColor: 'red'
              backgroundColor: '#FF6633',
              title: '1'
            }
          ],
          pages: 'result',   // 这里有三个选项：create、select、result 对应3个页面
        }
      },
      // 结果页面被选中的时间块的id
      idOfSelectTime: ''
    }
  },
  methods: {
    // 与日历组件通信，时时更新this.datasToCalendar.calendarFunction.events
    getTimeUnit(timeUnit) {
      this.datasToCalendar.calendarFunction.events = timeUnit;
    },
    // 结果页面点击时间块的时候，子组件发送时间块的id给父组件
    getTimeUnitId(id) {
      this.idOfSelectTime = id;
    },
    // 动态调整日历的格式：实现的时候与表单进行绑定（第三期任务）
    changeCalendarFormat() {
      let formatapi = this.datasToCalendar.calendarFormat;
  
      formatapi.slotDuration = '00:30' 
      formatapi.defaultTimedEventDuration = '00:30'
      formatapi.slotMinTime = "10:00:00"
      formatapi.slotMaxTime = "20:00:00"
      formatapi.businessHours = {
        daysOfWeek: [ 1, 2, 3, 5, 6, 0],
        startTime: '11:00',
        endTime: '19:00', 
      }
      formatapi.hiddenDays = [1,4]
      formatapi.validRange = {
        start: '2020-11-11',
        end: '2020-11-19'
      }
    }
  }
}
</script>
