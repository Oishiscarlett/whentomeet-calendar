# 日历文档

## 一、前言

1. 本次使用的组件是 fullcalendar ；

   官方文档网址：https://fullcalendar.io/docs；

   网上的中文文档（内容比较粗略）：https://blog.csdn.net/rchm8519/article/details/43306011

2. 在该日历组件中，点击日历当中的时间是创建一件事件；因此在使用这个组件的时候，需要注意：

   ==我们的 timeUnit 这个概念是基本上是对应与这个组件的 event 概念==

   也就是说，在该组件上创建一件事就是我们所说的选择一个时间。



## 二、数据格式

1. 时间id的格式：年-月-日-时：分：秒-星期   

   ​									2020-01-06-09:00:00-5 

   是使用calendar-utils文件中的函数解析的



## 三、创建事件页面功能

### 1. 调整日历格式

​	暂未实现

### 2. 选择时间

1. 点击创建一个时间

   ```js
   // 选择时间 并生成相应的时间记录 对应回调函数
   select: this.handleDateSelect
       
   // 创建已经选择的时间
       handleDateSelect(selectInfo) {
         let calendarApi = selectInfo.view.calendar
         calendarApi.unselect() // clear date selection
         calendarApi.addEvent({
           // 此处的id就是对应timeUnit的id
           id: createTimeUnitId(selectInfo.start),
           start: selectInfo.start,
         })
       },
   ```

2. 点击页面上的格子可以选择时间，规定不允许拖动，即一次只能选中一格

   ```js
   // 不允许拖动 对应属性
   
   // 设置默认显示的时间间隔
   slotDuration: '01:00', // 1 hours
   // 选择的时间的默认间隔（应与slotDuration保持一致）
   defaultTimedEventDuration: '01:00',
       
   // 以上两个属性值应该保持一致
   ```

3. 点击已经生成的时间块，取消选择

   ```js
   //取消已选择的时间 对应回调函数
   eventClick: this.handleEventClick
   
   handleEventClick(clickInfo) {
      // 如果是创建事件页面,点击就是取消选择
      clickInfo.event.remove()
   }
   ```

### 3. 传数据

1. 传数据给后端 只要把event数组中的所有的id 传过去



## 四、选择时间页面功能

### 1. 页面渲染

1. 将获取到的 timeUnit id 解析成需要的时间格式：'2020-11-01T10:00:00'

   用calendar-utils中的函数

2. 根据这个格式和id创建event

   ```js
   // 事件属性
   id: timeUnit id
   start: 由 timeUnit 解析而来的
   groupId: 'hostSelect'
   ```

3. 要将日历的 selectable 属性改成：false

### 2. 与会者选择时间

1. 用户只能在发起者选择过的时间上做选择，并且点击之后，时间块的颜色会发生改变（颜色用\#003399）

   ```js
   	  //此处代码是写在回调函数handleEventClick中
   
         // 如果是填写事件页面
         // 先判断当前时间是 inviteeSelect 或者 hostSelect
         if(clickInfo.event.groupId === 'hostSelect'){
           clickInfo.event.remove();
           clickInfo.view.calendar.addEvent({
             id: clickInfo.event.id,
             start: clickInfo.event.start,
             groupId: 'inviteeSelect',
             backgroundColor: 'red'
           })
         }else{
           clickInfo.event.remove();
           clickInfo.view.calendar.addEvent({
             id: clickInfo.event.id,
             start: clickInfo.event.start,
             groupId: 'hostSelect',
             backgroundColor: '#3788d8'
           })
         }
   ```

### 3. 传数据

1. 传数据给后端，传数据之前，需要先判断event数组中的对象的groupId；将所有groupId等于inviteeSelect的所有对象 id 传过去



## 五、结果页面功能

### 1. 页面渲染

1. 将获取到的 timeUnit id 解析成需要的时间格式：'2020-11-01T10:00:00'

   用calendar-utils中的函数

2. 根据这个格式和id创建time（event）

   ```js
   // 事件属性
   id: timeUnit id
   start: 由 timeUnit 解析而来的
   groupId: 'hostSelect'
   
   // 以下三个属性用来设置不同的颜色，标注最佳时间和推荐时间
   // 若不是 最佳时间或推荐时间 不需要设置
   // 最佳时间的颜色：#009933
   // 推荐时间的颜色：#FAAD14
   backgroundColor: 
   borderColor：
   textColor：//可以不用改
   ```

3. 要将日历的 selectable 属性改成：false

4. 不能参与人数渲染：

   ```js
   // 在创建事件的时候将事件的title 设置为不能参与的人数 数字的颜色：#cc0000
   title: 
   ```

### 2. 点击时间，查看详情

1. 返回点击时间块的id，根据id查找数据，渲染

   ```js
   //此处代码是写在回调函数handleEventClick中
   
   // 若是结果页面，返回点击时间的id，根据id查找数据，渲染
    return clickInfo.event.id;
   ```

   