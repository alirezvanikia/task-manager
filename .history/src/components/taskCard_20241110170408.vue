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
            <div class="task-manager-card__timer">
                {{ formattedTime }} <!-- Show current time -->
            </div>
            <div class="task-manager-card__timer">
                {{ completedElapsedTime }} <!-- Show final or current time -->
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
        // completedElapsedTime: { // تعریف prop برای زمان سپری شده نهایی
        //     type: Number,
        //     default: 0,
        // },
        task: Object // Transfer the complete information of the task to the card component
    },

    data() {
        return {
            elapsedTime: 0, // Currently elapsed time
            timer: null, // Variables for save time
            isPaused: false, // Timer status
            //completedElapsedTime: null, // Save the final elapsed time
            ShowedDetail: false,
            a: 0,
        };
    },
    computed: {
        formattedTime() {
            // Format to current time
            return this.formatTime(this.elapsedTime);
        },
        // formattedCompletedTime() {
        //     // Final elapsed time format
        //     return this.formatTime(this.completedElapsedTime);
        // }
    },
    watch: {
        // If the task is completed, stop the timer
        isCompleted(newValue) {
            if (newValue) {
                clearInterval(this.timer);
                //this.completedElapsedTime = this.elapsedTime;
            }
        }
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

            const dateObj = new Date();
            const minutes = dateObj.getMinutes();
            const hours = dateObj.getHours();
            const day = dateObj.getDate();
            const month = dateObj.getMonth() + 1;
            const year = dateObj.getFullYear();

            this.compeletedTime = ` ${hours}:${minutes} - ${year}/${month}/${day}`;
            this.$emit('end-task', this.compeletedTime, this.id, this); // Send completion event to parent
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