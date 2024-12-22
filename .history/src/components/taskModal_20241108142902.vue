<template>
    <div class="task-manager-modal">
        <button @click="$emit('close')" class="btn task-manager-modal__btn">
            <img src="./../icons/cross-small.svg" class="task-manager-icon" alt="cross-small">
        </button>
        <div class="task-manager-modal__content">
            <div class="task-manager-modal__content-item">
                <div class="task-manager-modal__content-item-title">
                    نام تسک
                </div>
                <div class="task-manager-modal__content-item-desc">
                    {{ taskTitle }}
                </div>
            </div>
            <div class="task-manager-modal__content-item">
                <div class="task-manager-modal__content-item-title">
                    دسته بندی تسک
                </div>
                <div class="task-manager-modal__content-item-desc">
                    {{ taskCategory }}
                </div>
            </div>
            <div v-if="taskType" class="task-manager-modal__content-item">
                <div class="task-manager-modal__content-item-title">
                    نوع تسک
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
        task: {
            type: Object,
            required: true,
        },
        taskTitle: {
            type: String,
            required: true, // Make sure title is passed in
        },
        taskCategory: {
            type: String,
            required: true, // Make sure title is passed in
        },
        taskType: {
            type: String,
            required: true, // Make sure title is passed in
        },
        taskLevel: {
            type: String,
            required: true, // Make sure title is passed in
        },
        taskDescription: {
            type: String,
            required: true, // Make sure title is passed in
        },
    },
};
</script>