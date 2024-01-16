<template>
  <div class="flex justify-between px-2 mb-3 text-white">
    <h1 class="font-bold text-3xl">Kanban Board</h1>
    <button
      class="font-bold"
      @click="isColumnModalVisible = !isColumnModalVisible"
    >
      Add Column
    </button>
  </div>
  <Modal
    :modal-active="isColumnModalVisible"
    :heading="`${ACTIONS.ADD_COLUMN.split('_').join(' ')}`"
    @close-modal="isColumnModalVisible = !isColumnModalVisible"
  >
    <ColumnForm
      :action="ACTIONS.ADD_COLUMN"
      @close-modal="isColumnModalVisible = !isColumnModalVisible"
    />
  </Modal>
</template>

<script setup lang="ts">
import { ref } from "vue";

import ColumnForm from "./ColumnForm.vue";
import { ACTIONS } from "../types";
import Modal from "./common/Modal.vue";

const isColumnModalVisible = ref(false);
</script>
