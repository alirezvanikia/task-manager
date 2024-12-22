<template>
    <div :class="{ active: !isCompleted }" class="task-manager-card">
        <!-- Task card with active class if not completed -->
        <div class="task-manager-card__content">
            <div class="task-manager-card__title">
                {{ title }}
            </div>
        </div>
        <div class="task-manager-card__informations">
            <div class="task-manager-card__date">
                {{ completedTime }} <!-- Show completion time if available -->
            </div>
            <div v-if="!isCompleted" class="task-manager-card__timer">
                {{ formattedTime }} <!-- Show current time -->
            </div>
            <div v-else class="task-manager-card__timer">
                {{ completedElapsedTime || 'تایم ثبت نشده' }} <!-- Show final or current time -->
            </div>
            <div class="task-manager-card__actions" v-if="!isCompleted">
                <div v-if="!isPaused" @click="pauseTask" class="btn btn--green btn--square">
                    <img src="../icons/pause.svg" alt="pause">
                </div>
                <div v-else @click="resumeTask" class="btn btn--green btn--square">
                    <img src="../icons/play.svg" alt="play">
                </div>
                <div @click="endTask" class="btn btn--green btn--square">
                    <img src="../icons/check.svg" alt="checkbox">
                </div>
            </div>
            <div class="task-manager-card__actions" v-else>
                <div @click="detailTask" class="btn btn--green btn--square">
                    <img src="../icons/exclamation.svg" alt="exclamation">
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    props: {
        title: {
            type: String,
            required: true,
        },
        type: {
            type: String,
            required: true,
        },
        category: {
            type: String,
            required: true,
        },
        isCompleted: {
            type: Boolean,
            default: false,
        },
        id: {
            type: Number,
            required: true,
        },
        completedTime: { // Prop to display completion time
            type: String,
            default: ''
        },
        completedElapsedTime: { // The prop definition for the final elapsed time
            type: String,
            default: ""
        },
        task: Object // Transfer the complete information of the task to the card component
    },

    data() {
        return {
            elapsedTime: 0, // Currently elapsed time
            timer: null, // Variables for save time
            isPaused: false, // Timer status
            ShowedDetail: false,
        };
    },
    computed: {
        formattedTime() {
            // Format to current time
            return this.formatTime(this.elapsedTime);
        },
    },
    mounted() {
        // Start the timer when building the component if the task is not completed
        if (!this.isCompleted) {
            this.startTimer();
        }
    },
    beforeUnmount() {
        // Clear the timer before destroying the component
        clearInterval(this.timer);
    },
    methods: {
        // Start timer
        startTimer() {
            if (!this.isCompleted) {
                this.timer = setInterval(() => {
                    this.elapsedTime++;
                }, 1000);
            }
        },
        // Stop the timer and change the state
        pauseTask() {
            clearInterval(this.timer);
            this.isPaused = true;
            this.$emit('pause-task', this.id);
        },
        // Restart the timer and change the state
        resumeTask() {
            this.startTimer();
            this.isPaused = false;
            this.$emit('resume-task', this.id);
        },
        // Finish the task and save the final time
        endTask() {
        clearInterval(this.timer);
        
        // ایجاد تاریخ به فرمت صحیح `YYYY/MM/DD`
        const dateObj = new Date();
        const year = dateObj.getFullYear();
        const month = String(dateObj.getMonth() + 1).padStart(2, '0'); // تنظیم ماه به صورت دو رقمی
        const day = String(dateObj.getDate()).padStart(2, '0'); // تنظیم روز به صورت دو رقمی
        const hours = String(dateObj.getHours()).padStart(2, '0'); // تنظیم ساعت به صورت دو رقمی
        const minutes = String(dateObj.getMinutes()).padStart(2, '0'); // تنظیم دقیقه به صورت دو رقمی

        // فرمت نهایی `YYYY/MM/DD - HH:MM`
        this.completedTime = `${year}/${month}/${day} - ${hours}:${minutes}`;

        // ارسال زمان تکمیل به همراه ID تسک و زمان سپری شده به کامپوننت والد
        this.$emit('end-task', this.completedTime, this.id, this.formattedTime);
    },
        // Display task details
        detailTask() {
            this.$emit('detail-task', this.id); // Pass the task ID to the parent for display
        },
        // format to time (hour:minute:second)
        formatTime(timeInSeconds) {
            const hours = Math.floor(timeInSeconds / 3600);
            const minutes = Math.floor((timeInSeconds % 3600) / 60);
            const seconds = timeInSeconds % 60;
            return `${String(hours).padStart(2, '0')}:${String(minutes).padStart(2, '0')}:${String(seconds).padStart(2, '0')}`;
        }
    },
};
</script>