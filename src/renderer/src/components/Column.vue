<template>
  <DropZone @drop-data="onDrop">
    <Draggable
      :transferData="{
        type: TYPES.COLUMN,
        columnId: column.columnId,
      }"
      class="p-2 w-64 bg-gray-100 shadow-lg shadow-slate-800 rounded"
    >
      <div class="flex justify-between items-center">
        <div class="flex gap-2 items-center">
          <span class="text-xl font-semibold overflow-x-auto w-40 py-1">
            {{ column.name }}
          </span>

          <button @click="setSelectedColumn(column, ACTIONS.UPDATE_COLUMN)">
            &#9998;
          </button>
          <button
            class="font-bold"
            @click="setSelectedColumn(column, ACTIONS.DELETE_COLUMN)"
          >
            &#88;
          </button>
        </div>
        <button class="text-2xl font-bold" @click="addTask(column.columnId)">
          +
        </button>
      </div>
      <div class="flex flex-col gap-2 overflow-y-auto h-[35rem] p-2">
        <Task
          v-for="task of column.tasks"
          :key="task.taskId"
          :task="task"
          :columnId="column.columnId"
        />
      </div>
    </Draggable>
  </DropZone>

  <Modal
    :modal-active="isColumnModalVisible"
    :heading="`${ACTIONS.UPDATE_COLUMN.split('_').join(' ')}`"
    @close-modal="toggleColumnModal()"
  >
    <ColumnForm
      :column="column"
      :action="ACTIONS.UPDATE_COLUMN"
      @close-modal="toggleColumnModal()"
    />
  </Modal>

  <Modal
    :modal-active="isDeleteColumnModalVisible"
    :heading="`${ACTIONS.DELETE_COLUMN.split('_').join(' ')}`"
    @close-modal="toggleDeleteColumnModal()"
  >
    <div class="p-2">
      <span> Are you sure you want to delete {{ column && column.name }}?</span>
      <div class="flex justify-around pt-3">
        <button @click="toggleDeleteColumnModal">No</button>
        <button @click="deleteColumn">Yes</button>
      </div>
    </div>
  </Modal>

  <Modal
    :modal-active="isTaskModalVisible"
    :heading="`${taskAction.split('_')[0]} Task`"
    @close-modal="isTaskModalVisible = !isTaskModalVisible"
  >
    <TaskForm
      :column-id="selectedColumnId"
      :action="taskAction"
      @close-modal="isTaskModalVisible = !isTaskModalVisible"
    />
  </Modal>
</template>
<script setup lang="ts">
import { ref } from "vue";

import type { Column, TRANSFER_DATA } from "../types";
import Draggable from "./common/Draggable.vue";
import DropZone from "./common/DropZone.vue";
import Task from "./Task.vue";
import { TYPES, ACTIONS } from "../types";
import Modal from "./common/Modal.vue";
import TaskForm from "./TaskForm.vue";
import ColumnForm from "./ColumnForm.vue";
import kanbanStore from "../stores/kanbanStore";

const props = defineProps<{
  column: Column;
}>();

// COLUMN LOGIC
let selectedColumn = ref<Column>();
let isColumnModalVisible = ref(false);
let isDeleteColumnModalVisible = ref(false);

function setSelectedColumn(column: Column, action: ACTIONS) {
  selectedColumn.value = column;
  if (action === ACTIONS.UPDATE_COLUMN) {
    toggleColumnModal();
  } else if (action === ACTIONS.DELETE_COLUMN) {
    toggleDeleteColumnModal();
  }
}

function toggleColumnModal() {
  isColumnModalVisible.value = !isColumnModalVisible.value;
}

function toggleDeleteColumnModal() {
  isDeleteColumnModalVisible.value = !isDeleteColumnModalVisible.value;
}

function deleteColumn() {
  kanbanStore.deleteColumn(selectedColumn.value?.columnId!);
  toggleDeleteColumnModal();
}

function onDrop(transferData: TRANSFER_DATA) {
  if (transferData.type === TYPES.COLUMN && transferData.columnId) {
    kanbanStore.moveColumn(transferData.columnId, props.column.columnId);
  } else if (transferData.type === TYPES.TASK && transferData.taskId) {
    kanbanStore.moveTask(transferData.taskId, props.column.columnId);
  }
}

// TASK LOGIC
let selectedColumnId = ref("");
let taskAction = ref<ACTIONS.ADD_TASK | ACTIONS.UPDATE_TASK>(ACTIONS.ADD_TASK);
let isTaskModalVisible = ref(false);

function addTask(columnId: Column["columnId"]) {
  selectedColumnId.value = columnId;
  taskAction.value = ACTIONS.ADD_TASK;
  toggleTaskModal();
}

function toggleTaskModal() {
  isTaskModalVisible.value = !isTaskModalVisible.value;
}
</script>
