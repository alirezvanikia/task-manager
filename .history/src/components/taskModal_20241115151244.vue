<template>
    <div class="task-manager-modal">
        <!-- modal close button that sends the 'close' event to the parent -->
        <button @click="$emit('close')" class="btn task-manager-modal__btn">
            <img src="./../icons/cross-small.svg" class="task-manager-icon" alt="cross-small">
        </button>
        <div class="task-manager-modal__content">
            <div class="task-manager-modal__content-item">
                <div class="task-manager-modal__content-item-title">
                    Task Name
                </div>
                <div class="task-manager-modal__content-item-desc">
                    {{ taskTitle }}
                </div>
            </div>
            <div class="task-manager-modal__content-item">
                <div class="task-manager-modal__content-item-title">
                   Task Category
                </div>
                <div class="task-manager-modal__content-item-desc">
                    {{ taskCategory }}
                </div>
            </div>
            <div v-if="taskType" class="task-manager-modal__content-item">

                <div class="task-manager-modal__content-item-title">
                </div>
                <div class="task-manager-modal__content-item-desc">
                    {{ taskType }}
                </div>
            </div>
            <div v-if="taskLevel" class="task-manager-modal__content-item">
                <div class="task-manager-modal__content-item-title">
                    سطح سختی تسک
                </div>
                <div class="task-manager-modal__content-item-desc">
                    {{ taskLevel }}
                </div>
            </div>
            <div v-if="taskDescription" class="task-manager-modal__content-item">
                <div class="task-manager-modal__content-item-title">
                    توضیحات تسک
                </div>
                <div class="task-manager-modal__content-item-desc">
                    {{ taskDescription }}
                </div>
            </div>
        </div>
    </div>

</template>

<script>
export default {
    props: {
        // Get the task object as prop
        task: {
            type: Object,
            required: true,
        },
        taskTitle: {
            type: String,
            required: true,
        },
        taskCategory: {
            type: String,
            required: true,
        },
        taskType: {
            type: String,
            required: true,
        },
        taskLevel: {
            type: String,
            required: true,
        },
        taskDescription: {
            type: String,
            required: true,
        },
    },
};
</script>