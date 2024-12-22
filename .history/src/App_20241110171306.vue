<template>
  <div class="task-manager" ref="taskManagerDiv">
    <div class="container">
      <header class="header">
        <div class="header__logo">
          <img class="header__image" src="./media/rtl-logo.png" alt="logo">
        </div>
        <div class="header__avatar">
          <div class="header__name">
            ali Kia
          </div>
          <img class="header__image" src="./media/avatar.png" alt="avatar">
        </div>
      </header>
      <div class="task-manager-area row">
        <div class="col-lg-6">
          <div class="task-manager-box task-manager-box__attendance-time">
            <div class="task-manager-title">
              <div class="task-manager-title__icon">
                <img src="./icons/cookie.svg" class="task-manager-icon" alt="cookie">
              </div>
              <div class="task-manager-title__text">
                شروع به کار
                <span v-if="!isAttendance">
                  از ساعت {{ this.compeletedTime }}
                </span>
              </div>
            </div>
            <div class="task-manager-box__attendance-btn">
              <button @click="attendance" v-if="isAttendance" class="btn btn--green me-auto">ورود</button>
              <button @click="absence" v-else class="btn btn--green me-auto">خروج</button>
            </div>
          </div>
          <div class="task-manager-box">
            <div class="task-manager-values">
              <div class="task-manager-title">
                <div class="task-manager-title__icon">
                  <img src="./icons/add-task-box.svg" class="task-manager-icon" alt="hand-scanner">
                </div>
                <div class="task-manager-title__text">
                  جعبه تسک
                </div>
              </div>
              <div class="row">
                <div class="col-lg-12">
                  <div class="task-manager-input">
                    <input v-model="newTaskTitle" type="text" placeholder="عنوان تسک"
                      class="task-manager-input__inner" />
                    <img src="./icons/pencil.svg" class="task-manager-icon" alt="pencil">
                  </div>
                </div>
                <div class="col-lg-4">
                  <div class="task-manager-input">
                    <select class="task-manager-input__inner" v-model="newTaskCategory"
                      aria-label="Default select example">
                      <option selected Default value="راست چین">راست چین</option>
                      <option value="هاست">هاست</option>
                      <option value="جلسه">جلسه</option>
                      <option value="متفرقه">متفرقه</option>
                    </select>
                    <img src="./icons/file-search.svg" class="task-manager-icon" alt="pencil">
                  </div>
                </div>
                <div class="col-lg-4">
                  <div class="task-manager-input">
                    <input type="text" v-model="newTaskType" placeholder="نوع تسک" class="task-manager-input__inner">
                    <img src="./icons/chart-network.svg" class="task-manager-icon" alt="chart-network">
                  </div>
                </div>
                <div class="col-lg-4">
                  <div class="task-manager-input">
                    <input type="number" v-model="newTaskLevel" placeholder="درجه سختی"
                      class="task-manager-input__inner">
                    <img src="./icons/tornado.svg" class="task-manager-icon" alt="tornado">
                  </div>
                </div>
                <div class="col-lg-12">
                  <div class="task-manager-input">
                    <textarea class="task-manager-input__inner" v-model="newTaskDescription" placeholder="توضیحات تسک"
                      id="exampleFormControlTextarea1" rows="3"></textarea>
                    <img src="./icons/microphone-alt.svg" class="task-manager-icon" alt="chart-network">
                  </div>
                </div>
                <div class="task-manager__action">
                  <button @click="addTask" class="btn btn--green me-auto">ثبت</button>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div class="col-lg-6">
          <div class="task-manager-box h-100">
            <div class="task-manager-actives">
              <div class="task-manager-title">
                <div class="task-manager-title__icon">
                  <img src="./icons/recycle.svg" class="task-manager-icon" alt="hand-scanner">
                </div>
                <div class="task-manager-title__text">
                  تسک های فعال
                </div>
              </div>
            </div>
            <div class="row">
              <div class="col-lg-12">
                <div class="task-manager-card-container">
                  <!--
                  create cards and add 'activeTasks' for all
                  - `key`: every tasks key
                  - `isCompleted`: tasks status
                  - `@end-task="completeTask(task.id)`: call completed task with id after end task
                -->
                  <taskCard v-for="task in activeTasks" :key="task.id" :title="task.title"
                    :isCompleted="task.isCompleted" :id="task.id" @end-task="completeTask"
                    :completedTime="task.completedTime" @pause-task="pauseTask" @resume-task="resumeTask" />
                  <!-- For when the box is empty-->
                  <div class="task-manager-box__empty" v-if="activeTasks.length == 0">
                    درحال حاضر مشغول انجام کاری نیستید!
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div class="col-lg-12">
          <div class="task-manager-box mt-4">
            <div class="task-manager-history">
              <div class="task-manager-title">
                <div class="task-manager-title__icon">
                  <img src="./icons/time-twenty-four.svg" class="task-manager-icon" alt="time-twenty-four">
                </div>
                <div class="task-manager-title__text">
                  تاریخچه تسک ها
                </div>
              </div>
              <div class="task-manager-card-container">
                <div class="task-manager-card-container">
                  <taskCard v-for="task in completedTasks" :key="task.id" :title="task.title" :type="task.type"
                    :category="task.category" :isCompleted="task.isCompleted" :id="task.id"
                    :completedTime="task.completedTime" @detail-task="showTaskDetails(task.id)"
                    @end-task="completeTask" />
                </div>

              </div>
              <!-- For when the box is empty-->
              <div class="task-manager-box__empty" v-if="completedTasks.length == 0">
                تسک انجام شده ای در تاریخچه شما یافت نشد!
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <taskModal v-if="isModalVisible" :taskTitle="selectedTaskTitle" :taskCategory="selectedTaskCategory"
      :taskType="selectedTaskType" :taskLevel="selectedTaskLevel" :taskDescription="selectedTaskDescription"
      @close="hideTaskDetails" />

  </div>
</template>

<script>
//import axios from 'axios';
import taskCard from './components/taskCard.vue';
import TaskModal from './components/taskModal.vue';

export default {
  components: { taskCard, TaskModal },
  data() {
    return {
      newTaskTitle: '', // Save input value
      newTaskType: '', // Save input type
      newTaskLevel: '', // Save input type
      tasks: [], // Tasks list 
      newTaskCategory: 'راست چین', // مقدار پیش‌فرض برای نمایش «راست چین»
      isModalVisible: false, // متغیر برای کنترل نمایش مدال
      selectedTaskTitle: '',
      selectedTaskCategory: '',
      selectedTaskType: '',
      selectedTaskLevel: '',
      selectedTaskDescription: '',
      isAttendance: true,
      isAbsence: false
    };
  },
  computed: {
    activeTasks() {
      return this.tasks.filter(task => !task.isCompleted);
    },
    completedTasks() {
      return this.tasks.filter(task => task.isCompleted);
    },
  },
  methods: {
    fetchTasks() {
      fetch('/jsonData.json')
        .then(response => response.json())
        .then(data => {
          // Assuming each task has `isCompleted` property, if not, initialize it
          this.tasks = data.map(task => ({
            ...task, title: task.title,
            category: task.category,
            type: task.type,
            level: task.level,
            description: task.description,
            completedDate: task.completedDate,
            isCompleted: task.completedTime
              ? true : false
          })
          );
        })
        .catch(error => console.error('Error fetching tasks:', error));
    },
    addTask() {
      // افزودن تسک جدید به لیست تسک‌ها
      if (this.newTaskTitle) {
        this.tasks.push({
          id: Math.floor(Math.random() * 1000000), // تولید شناسه یکتا برای تسک
          title: this.newTaskTitle,
          type: this.newTaskType,
          category: this.newTaskCategory,
          level: this.newTaskLevel,
          description: this.newTaskDescription,
          isCompleted: false // وضعیت تکمیل نشده
        });
        // بازنشانی مقادیر فیلدهای فرم تسک جدید
        this.newTaskTitle = '';
        this.newTaskType = '';
        this.newTaskCategory = 'راست چین';
        this.newTaskLevel = '';
        this.newTaskDescription = '';
      }
    },
    completeTask(completedTime, id, elapsedTime) {

      console.log();
      
      // تابعی برای علامت زدن تسک به عنوان تکمیل شده
      const task = this.tasks.find(task => task.id === id); // پیدا کردن تسکی با شناسه‌ی مشخص
      if (task) { // بررسی وجود تسک
        task.isCompleted = true; // تنظیم وضعیت تسک به تکمیل شده
        task.completedTime = completedTime; // ثبت زمان تکمیل
      }
    },
    pauseTask(id) {
      console.log(`Paused task with ID: ${id}`);
    },
    resumeTask(id) {
      console.log(`Resumed task with ID: ${id}`);
    },
    detailTask(id) {
      console.log(`Detail for task with ID: ${id}`);
      const taskManagerDiv = this.$refs.taskManagerDiv;

      // Log the div to the console
      console.log(taskManagerDiv);
    },
    attendance() {
      this.isAttendance = false
      this.isAbsence = true

      const dateObj = new Date();
      const minutes = dateObj.getMinutes();
      const hours = dateObj.getHours();
      // فرمت کردن دقیقه‌ها به صورت دو رقمی
      const formattedMinutes = String(minutes).padStart(2, '0');
      this.compeletedTime = ` ${hours}:${formattedMinutes} `;
    },
    absence() {
      this.isAttendance = true
      this.isAbsence = false

      const dateObj = new Date();
      const minutes = dateObj.getMinutes();
      const hours = dateObj.getHours();
      // فرمت کردن دقیقه‌ها به صورت دو رقمی
      const formattedMinutes = String(minutes).padStart(2, '0');
      this.compeletedTime = ` ${hours}:${formattedMinutes} `;
      alert(`ساعت پایان کار شما: ${this.compeletedTime}`)
    },
    removeCompletedTask(id) {
      this.tasks = this.tasks.filter(task => task.id !== id);
    },
    showTaskDetails(id) {
      const task = this.tasks.find(task => task.id === id);
      if (task) {
        this.selectedTaskTitle = task.title;
        this.selectedTaskCategory = task.category;
        this.selectedTaskType = task.type;
        this.selectedTaskLevel = task.level;
        this.selectedTaskDescription = task.description;

        this.isModalVisible = true;
      }
      console.log(`Show details for task with ID: ${id}`);
      const taskManagerDiv = this.$refs.taskManagerDiv;
      let overlayDiv = taskManagerDiv.querySelector('.overlay');

      if (!overlayDiv) {
        overlayDiv = document.createElement('div');
        overlayDiv.classList.add('overlay');
        overlayDiv.addEventListener('click', this.hideTaskDetails);
        taskManagerDiv.appendChild(overlayDiv);
      }
    },
    hideTaskDetails() {
      this.isModalVisible = false;
      const taskManagerDiv = this.$refs.taskManagerDiv;
      const overlayDiv = taskManagerDiv.querySelector('.overlay');
      if (overlayDiv) {
        taskManagerDiv.removeChild(overlayDiv);
      }
    },
    resetFormFields() {
      this.newTaskTitle = '';
      this.newTaskType = '';
      this.newTaskCategory = 'راست چین';
      this.newTaskLevel = '';
      this.newTaskDescription = '';
    },
  },
  created() {
    this.fetchTasks(); // فراخوانی داده‌ها هنگام لود شدن صفحه
  },
  mounted() {
    this.fetchTasks();
  }
};
</script>