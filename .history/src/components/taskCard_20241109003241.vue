<template>
    <div :class="{ active: !isCompleted }" class="task-manager-card"> <!-- Task card with active class if not completed -->
        <div class="task-manager-card__content">
            <div class="task-manager-card__title">
                {{ title }}
            </div>
        </div>
        <div class="task-manager-card__informations">
            <div class="task-manager-card__date">
                {{ completedTime }}
            </div>
            <div v-if="!isCompleted" class="task-manager-card__timer">
                {{ formattedTime }}
            </div>
            <div v-else class="task-manager-card__timer">
                {{ isCompleted ? formattedCompletedTime : formattedTime }}
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
                <div  @click="detailTask" class="btn btn--green btn--square">
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
        completedTime: { // اضافه کردن completedTime به عنوان prop
            type: String,
            default: ''
        },
        task: Object // Pass the task data to the card component
    },

    data() {
        return {
            elapsedTime: 0, // Spend time
            timer: null, // Variables for save time
            isPaused: false, // Timer status
            completedElapsedTime: null, // زمان نهایی سپری شده
            ShowedDetail: false,
        };
    },
    computed: {
        formattedTime() {
            // فرمت زمان جاری
            return this.formatTime(this.elapsedTime);
        },
        formattedCompletedTime() {
            // فرمت زمان نهایی سپری شده
            return this.formatTime(this.completedElapsedTime);
        }
    },
    watch: {
        // اگر تسک تکمیل شد، تایمر را متوقف کن
        isCompleted(newValue) {
            if (newValue) {
                clearInterval(this.timer);
            }
        }
    },
    mounted() {
        // Start timer when run mount
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

        // Pause timer & remove pause button
        pauseTask() {
            clearInterval(this.timer);
            this.isPaused = true;
            this.$emit('pause-task', this.id);
        },

        resumeTask() {
            this.startTimer();
            this.isPaused = false;
            this.$emit('resume-task', this.id);
        },

        endTask() {
            clearInterval(this.timer);
            this.completedElapsedTime = this.elapsedTime; // ذخیره زمان نهایی سپری‌شده

            const dateObj = new Date();
            const minutes = dateObj.getMinutes();
            const hours = dateObj.getHours();
            const day = dateObj.getDate();
            const month = dateObj.getMonth() + 1;
            const year = dateObj.getFullYear();

            this.compeletedTime = ` ${hours}:${minutes} - ${year}/${month}/${day}`;
            this.$emit('end-task', this.compeletedTime, this.id);
        },

        detailTask() {
            this.$emit('detail-task', this.id); // ارسال شناسه تسک به والد
        },

        formatTime(timeInSeconds) {
            const hours = Math.floor(timeInSeconds / 3600);
            const minutes = Math.floor((timeInSeconds % 3600) / 60);
            const seconds = timeInSeconds % 60;
            return `${String(hours).padStart(2, '0')}:${String(minutes).padStart(2, '0')}:${String(seconds).padStart(2, '0')}`;
        }
    },
};
</script>