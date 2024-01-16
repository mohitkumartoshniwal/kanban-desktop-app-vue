<template>
  <DropZone @drop-data="onTaskDrop">
    <Draggable
      class="shadow-[rgba(0,_0,_0,_0.24)_0px_3px_8px] rounded p-2"
      :transferData="{
        type: TYPES.TASK,
        currentColumnId: columnId,
        taskId: task.taskId,
      }"
    >
      <div>
        <div class="flex gap-2">
          <span class="font-bold"> {{ task.name }}</span>
          <div class="flex gap-1">
            <button class="text-sm font-bold" @click="toggleTaskModal()">
              &#9998;
            </button>
            <button class="text-sm font-bold" @click="toggleDeleteTaskModal()">
              &#88;
            </button>
          </div>
        </div>
        <div></div>
      </div>
      <p class="w-full mt-1 text-sm text-gray-400">
        {{ task.description }}
      </p>
    </Draggable>
  </DropZone>

  <Modal
    :modal-active="isTaskModalVisible"
    :heading="`${ACTIONS.UPDATE_TASK.split('_').join(' ')}`"
    @close-modal="toggleTaskModal()"
  >
    <TaskForm
      :columnId="columnId"
      :action="ACTIONS.UPDATE_TASK"
      :task="task"
      @close-modal="toggleTaskModal()"
    />
  </Modal>

  <Modal
    :modal-active="isDeleteTaskModalVisible"
    :heading="`${ACTIONS.DELETE_TASK.split('_').join(' ')}`"
    @close-modal="toggleDeleteTaskModal()"
  >
    <div class="p-2">
      <span> Are you sure you want to delete {{ task && task.name }}?</span>
      <div class="flex justify-around pt-3">
        <button @click="toggleDeleteTaskModal">No</button>
        <button @click="deleteTask">Yes</button>
      </div>
    </div>
  </Modal>
</template>
<script setup lang="ts">
import { ref } from "vue";
import {
  TYPES,
  type Column,
  type Task,
  ACTIONS,
  type TRANSFER_DATA,
} from "../types";
import DropZone from "./common/DropZone.vue";
import Draggable from "./common/Draggable.vue";
import kanbanStore from "../stores/kanbanStore";
import Modal from "./common/Modal.vue";
import TaskForm from "./TaskForm.vue";

const props = defineProps<{
  task: Task;
  columnId: Column["columnId"];
}>();

let isTaskModalVisible = ref(false);

function toggleTaskModal() {
  isTaskModalVisible.value = !isTaskModalVisible.value;
}

let isDeleteTaskModalVisible = ref(false);

function onTaskDrop(transferData: TRANSFER_DATA) {
  if (transferData.type === TYPES.TASK && transferData.taskId) {
    kanbanStore.moveTask(
      transferData.taskId,
      props.columnId,
      props.task.taskId
    );
  }
}

function deleteTask() {
  kanbanStore.deleteTask(props.columnId, props.task.taskId);
  toggleDeleteTaskModal();
}

function toggleDeleteTaskModal() {
  isDeleteTaskModalVisible.value = !isDeleteTaskModalVisible.value;
}
</script>
