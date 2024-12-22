<template>
  <div class="task-manager" ref="taskManagerDiv">
    <div class="container">
      <header class="header">
        <div class="header__logo">
          <img class="header__image" src="./media/rtl-logo.png" alt="logo">
        </div>
        <div class="header__avatar">
          <div class="header__name">
            Ali Kia
          </div>
          <img class="header__image" src="./media/avatar.png" alt="avatar">

          <div class="header__avatar-dropdown">
            <div class="header__avatar-logout">
              <div class="header__avatar-logout-icon">
                <img src="./icons/sign-in-alt.svg" class="task-manager-icon" alt="logout">
              </div>
              <div class="header__avatar-logout-text">
                Logout
              </div>
            </div>
          </div>
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
                Start Work
                <span v-if="!isAttendance">
                  From {{ this.compeletedTime }}
                </span>
              </div>
            </div>
            <div class="task-manager-box__attendance-btn">
              <button @click="attendance" v-if="isAttendance" class="btn btn--green me-auto">Check In</button>
              <button @click="absence" v-else class="btn btn--green me-auto">Check Out</button>
            </div>
          </div>
          <div class="task-manager-box">
            <div class="task-manager-values">
              <div class="task-manager-title">
                <div class="task-manager-title__icon">
                  <img src="./icons/add-task-box.svg" class="task-manager-icon" alt="add-task">
                </div>
                <div class="task-manager-title__text">
                  Task Box
                </div>
              </div>
              <div class="row">
                <div class="col-lg-12">
                  <div class="task-manager-input">
                    <input v-model="newTaskTitle" type="text" placeholder="Task Title"
                      class="task-manager-input__inner" />
                    <img src="./icons/pencil.svg" class="task-manager-icon" alt="pencil">
                  </div>
                </div>
                <div class="col-lg-4">
                  <div class="task-manager-input">
                    <select class="task-manager-input__inner" v-model="newTaskCategory"
                      aria-label="Default select example">
                      <option selected Default value="Right Aligned">Right Aligned</option>
                      <option value="Host">Host</option>
                      <option value="Meeting">Meeting</option>
                      <option value="Miscellaneous">Miscellaneous</option>
                    </select>
                    <img src="./icons/file-search.svg" class="task-manager-icon" alt="file-search">
                  </div>
                </div>
                <div class="col-lg-4">
                  <div class="task-manager-input">
                    <input type="text" v-model="newTaskType" placeholder="Task Type" class="task-manager-input__inner">
                    <img src="./icons/chart-network.svg" class="task-manager-icon" alt="chart-network">
                  </div>
                </div>
                <div class="col-lg-4">
                  <div class="task-manager-input">
                    <input type="number" v-model="newTaskLevel" placeholder="Difficulty Level"
                      class="task-manager-input__inner">
                    <img src="./icons/tornado.svg" class="task-manager-icon" alt="tornado">
                  </div>
                </div>
                <div class="col-lg-12">
                  <div class="task-manager-input">
                    <textarea class="task-manager-input__inner" v-model="newTaskDescription" placeholder="Task Description"
                      id="exampleFormControlTextarea1" rows="3"></textarea>
                    <img src="./icons/microphone-alt.svg" class="task-manager-icon" alt="microphone">
                  </div>
                </div>
                <div class="task-manager__action">
                  <button @click="addTask" class="btn btn--green me-auto">Submit</button>
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
                  <img src="./icons/recycle.svg" class="task-manager-icon" alt="recycle">
                </div>
                <div class="task-manager-title__text">
                  Active Tasks
                </div>
              </div>
            </div>
            <div class="row">
              <div class="col-lg-12">
                <div class="task-manager-card-container">
                  <taskCard v-for="task in activeTasks" :key="task.id" :title="task.title"
                    :isCompleted="task.isCompleted" :id="task.id" @end-task="completeTask"
                    :completedTime="task.completedTime" @pause-task="pauseTask" @resume-task="resumeTask" />
                  <!-- For when the box is empty-->
                  <div class="task-manager-box__empty" v-if="activeTasks.length == 0">
                    You are not currently working on anything!
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
                  <img src="./icons/time-twenty-four.svg" class="task-manager-icon" alt="history">
                </div>
                <div class="task-manager-title__text" @click="test">
                  Task History
                </div>
                <div class="task-manager-filter">
                  <label for="dateFilter">Filter by Date:</label>
                  <input type="date" id="dateFilter" v-model="selectedDate">
                </div>
              </div>
              <div class="task-manager-card-container">
                <taskCard v-for="task in filteredCompletedTasks" :key="task.id" :title="task.title" :type="task.type"
                  :category="task.category" :isCompleted="task.isCompleted" :id="task.id"
                  :completedTime="task.completedTime" :completed-elapsed-time="task.elapsedTime"  :completed-elapsed-hour="task.elapsedHour"
                  @detail-task="showTaskDetails(task.id)" @end-task="completeTask" />
              </div>
              <!-- Message if no tasks are found -->
              <div class="task-manager-box__empty" v-if="filteredCompletedTasks.length === 0">
                No task found on this date!
              </div>
              <!-- For when the box is empty-->
              <div class="task-manager-box__empty" v-if="completedTasks.length == 0">
                No completed tasks found in your history!
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
      newTaskTitle: '',
      newTaskType: '',
      newTaskLevel: '',
      tasks: [], // Tasks list 
      newTaskCategory: 'Right Aligned',
      isModalVisible: false, // Variable to control the display of the modal
      selectedTaskTitle: '',
      selectedTaskCategory: '',
      selectedTaskType: '',
      selectedTaskLevel: '',
      selectedTaskDescription: '',
      isAttendance: true, // Variable to control user presence
      isAbsence: false, // Variable to control user absence
      completedDates: [], // Array to store completed task dates

      selectedDate: null, // Selected date by user
    };
  },
  computed: {
    activeTasks() {
      return this.tasks.filter(task => !task.isCompleted);
    },
    completedTasks() {
      return this.tasks.filter(task => task.isCompleted);
    },

    filteredCompletedTasks() {
      if (this.selectedDate) {
        const formattedDate = this.selectedDate.replace(/-/g, '/');
        return this.completedTasks.filter(task =>
          task.completedTime && task.completedTime.startsWith(formattedDate)
        );
      }
      return this.completedTasks;
    }
  },
  methods: {
    fetchTasks() {
      fetch('/jsonData.json')
        .then(response => response.json())
        .then(data => {
          this.tasks = data.map(task => {
            task.isCompleted = false;
            return task;
          });
        });
    },
    addTask() {
      const newTask = {
        id: this.tasks.length + 1,
        title: this.newTaskTitle,
        type: this.newTaskType,
        category: this.newTaskCategory,
        level: this.newTaskLevel,
        description: this.newTaskDescription,
        isCompleted: false,
      };
      this.tasks.push(newTask);
      this.resetForm();
    },

    resetForm() {
      this.newTaskTitle = '';
      this.newTaskType = '';
      this.newTaskCategory = '';
      this.newTaskLevel = '';
      this.newTaskDescription = '';
    },

    attendance() {
      this.isAttendance = false;
    },

    absence() {
      this.isAttendance = true;
    },

    test() {
      this.completedDates = [...this.completedDates, 'Completed task on: ' + this.getCurrentDate()];
    },

    getCurrentDate() {
      return new Date().toLocaleDateString();
    },

    showTaskDetails(id) {
      const task = this.tasks.find(task => task.id === id);
      this.selectedTaskTitle = task.title;
      this.selectedTaskCategory = task.category;
      this.selectedTaskType = task.type;
      this.selectedTaskLevel = task.level;
      this.selectedTaskDescription = task.description;
      this.isModalVisible = true;
    },

    hideTaskDetails() {
      this.isModalVisible = false;
    },

    completeTask(id) {
      const task = this.tasks.find(task => task.id === id);
      task.isCompleted = true;
    },

    pauseTask(id) {
      const task = this.tasks.find(task => task.id === id);
      task.isPaused = true;
    },

    resumeTask(id) {
      const task = this.tasks.find(task => task.id === id);
      task.isPaused = false;
    }
  },
  created() {
    this.fetchTasks();
  }
};
</script>

