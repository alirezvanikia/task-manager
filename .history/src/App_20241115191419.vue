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

          <div class="header__avatar-dropdown">
            <div class="header__avatar-logout">
              <div class="header__avatar-logout-icon">
                <img src="./icons/sign-in-alt.svg" class="task-manager-icon" alt="cookie">
              </div>
              <div class="header__avatar-logout-text">
                Log out
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
                Start working
                <span v-if="!isAttendance">
                  from the hour {{ this.compeletedTime }}
                </span>
              </div>
            </div>
            <div class="task-manager-box__attendance-btn">
              <button @click="attendance" v-if="isAttendance" class="btn btn--green me-auto">Attendance at work</button>
              <button @click="absence" v-else class="btn btn--green me-auto">Exit from work</button>
            </div>
          </div>
          <div class="task-manager-box">
            <div class="task-manager-values">
              <div class="task-manager-title">
                <div class="task-manager-title__icon">
                  <img src="./icons/add-task-box.svg" class="task-manager-icon" alt="hand-scanner">
                </div>
                <div class="task-manager-title__text">
                  Task box
                </div>
              </div>
              <div class="row">
                <div class="col-lg-12">
                  <div class="task-manager-input">
                    <input v-model="newTaskTitle" type="text" placeholder="Task title"
                      class="task-manager-input__inner" />
                    <img src="./icons/pencil.svg" class="task-manager-icon" alt="pencil">
                  </div>
                </div>
                <div class="col-lg-4">
                  <div class="task-manager-input">
                    <select class="task-manager-input__inner" v-model="newTaskCategory"
                      aria-label="Default select example">
                      <option selected Default value="Main project">Main project</option>
                      <option value="Host project">Host project</option>
                      <option value="Meeting">Meeting</option>
                      <option value="Other">Other</option>
                    </select>
                    <img src="./icons/file-search.svg" class="task-manager-icon" alt="pencil">
                  </div>
                </div>
                <div class="col-lg-4">
                  <div class="task-manager-input">
                    <input type="text" v-model="newTaskType" placeholder="Task type" class="task-manager-input__inner">
                    <img src="./icons/chart-network.svg" class="task-manager-icon" alt="chart-network">
                  </div>
                </div>
                <div class="col-lg-4">
                  <div class="task-manager-input">
                    <input type="number" v-model="newTaskLevel" placeholder="Degree of difficulty"
                      class="task-manager-input__inner">
                    <img src="./icons/tornado.svg" class="task-manager-icon" alt="tornado">
                  </div>
                </div>
                <div class="col-lg-12">
                  <div class="task-manager-input">
                    <textarea class="task-manager-input__inner" v-model="newTaskDescription"
                      placeholder="Description of the task" id="exampleFormControlTextarea1" rows="3"></textarea>
                    <img src="./icons/microphone-alt.svg" class="task-manager-icon" alt="chart-network">
                  </div>
                </div>
                <div class="task-manager__action">
                  <button @click="addTask" class="btn btn--green ms-auto">Save</button>
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
                  Active tasks
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
                    You are not currently doing anything!
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
                <div class="task-manager-title__text" @click="test">
                  The history of tasks
                </div>
                <div class="task-manager-filter ms-auto">
                  <input class="task-manager-filter__input" placeholder="s" type="date" id="dateFilter" v-model="selectedDate">
                </div>
              </div>
              <div class="task-manager-card-container">
                <taskCard v-for="task in filteredCompletedTasks" :key="task.id" :title="task.title" :type="task.type"
                  :category="task.category" :isCompleted="task.isCompleted" :id="task.id"
                  :completedTime="task.completedTime" :completed-elapsed-time="task.elapsedTime"
                  :completed-elapsed-hour="task.elapsedHour" @detail-task="showTaskDetails(task.id)"
                  @end-task="completeTask" />
              </div>
              <div class="task-manager-box__empty" v-if="filteredCompletedTasks.length === 0">
                No tasks found for this date!
              </div>
              <!-- For when the box is empty-->
              <div class="task-manager-box__empty" v-if="completedTasks.length == 0">
                No completed tasks found in your history.
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
      newTaskCategory: 'Main project',
      isModalVisible: false, // Variable to control the display of the medal
      selectedTaskTitle: '',
      selectedTaskCategory: '',
      selectedTaskType: '',
      selectedTaskLevel: '',
      selectedTaskDescription: '',
      isAttendance: true, // Variable to control user presence
      isAbsence: false, // Variable to control user absence
      completedDates: [], // An array to store completed dates
      selectedDate: null, // The date selected by the user
    };
  },
  computed: {
    activeTasks() {
      // Calculation function to get the list of active tasks (tasks that have not been completed yet)
      return this.tasks.filter(task => !task.isCompleted);
    },
    completedTasks() {
      // Calculation function to get the list of completed tasks
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
          // Assuming each task has `isCompleted` property, if not, initialize it
          this.tasks = data.map(task => ({
            ...task, title: task.title,
            category: task.category,
            type: task.type,
            level: task.level,
            description: task.description,
            completedDate: task.completedDate,
            completedHour: task.completedDate,
            isCompleted: task.completedTime
              ? true : false
          })
          );
          this.extractCompletedDates(); // After fetching the data, we extract the dates

        })
        .catch(error => console.error('Error fetching tasks:', error));
    },
    addTask() {
      // Add a new task to the list of tasks
      if (this.newTaskTitle) {
        this.tasks.push({
          id: Math.floor(Math.random() * 1000000), // Generate a unique ID for the task
          title: this.newTaskTitle,
          type: this.newTaskType,
          category: this.newTaskCategory,
          level: this.newTaskLevel,
          description: this.newTaskDescription,
          isCompleted: false // Not completed status
        });
        this.newTaskTitle = '';
        this.newTaskType = '';
        this.newTaskCategory = 'Main project';
        this.newTaskLevel = '';
        this.newTaskDescription = '';
      }
    },
    completeTask(completedTime, completedHour, id, elapsedTime) {
      const task = this.tasks.find(task => task.id === id);
      if (task) {
        task.isCompleted = true;
        task.completedTime = completedTime;
        task.elapsedTime = elapsedTime;
        task.elapsedHour = completedHour;
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
      //const taskManagerDiv = this.$refs.taskManagerDiv;
    },
    attendance() {
      this.isAttendance = false
      this.isAbsence = true

      const dateObj = new Date();
      const minutes = dateObj.getMinutes();
      const hours = dateObj.getHours();
      // Format the minutes as two digits
      const formattedMinutes = String(minutes).padStart(2, '0');
      this.compeletedTime = ` ${hours}:${formattedMinutes} `;
    },
    absence() {
      this.isAttendance = true
      this.isAbsence = false

      const dateObj = new Date();
      const minutes = dateObj.getMinutes();
      const hours = dateObj.getHours();
      // Format the minutes as two digits
      const formattedMinutes = String(minutes).padStart(2, '0');
      this.compeletedTime = ` ${hours}:${formattedMinutes} `;
      alert(`Your work end time: ${this.compeletedTime}`)
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
      this.newTaskCategory = 'Main project';
      this.newTaskLevel = '';
      this.newTaskDescription = '';
    },
    extractCompletedDates() {
      this.completedDates = []; // First, we clear the array so that it doesn't contain duplicates if called again

      // Filtering completed tasks and adding their dates to completedDates
      this.tasks.forEach(task => {
        if (task.isCompleted) {
          this.completedDates.push(task.completedTime);// Adding the date to the array
          console.log(task.completedTime); 
        }
      });
    },
  },
  created() {
    this.fetchTasks(); // Fetching data when the page loads
  },
  mounted() {
    this.fetchTasks();
  }
};
</script>