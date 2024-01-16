<template>
  <Form
    ref="taskForm"
    :validation-schema="validationSchema"
    @submit="onSubmit"
    class="flex flex-col gap-2"
  >
    <label for="name">Name</label>
    <Field
      id="name"
      name="name"
      type="text"
      class="p-2 border-2 rounded-sm border-gray-300"
    />
    <ErrorMessage name="name" class="text-red-700" />
    <label for="description">Description</label>
    <Field
      id="description"
      name="description"
      type="text"
      class="p-2 border-2 rounded-sm border-gray-300"
    />
    <ErrorMessage name="description" class="text-red-700" />
    <button type="submit">Submit</button>
  </Form>
</template>
<script setup lang="ts">
import { onMounted, ref } from "vue";
import { Field, Form, ErrorMessage } from "vee-validate";
import { toTypedSchema } from "@vee-validate/zod";

import { taskFormSchema } from "../schemas";
import kanbanStore from "../stores/kanbanStore";
import { ACTIONS, type Column, type Task } from "../types";

const validationSchema = toTypedSchema(taskFormSchema);

const emit = defineEmits<{
  (e: "close-modal"): void;
}>();

const props = defineProps<{
  columnId: Column["columnId"];
  action: ACTIONS;
  task?: Task;
}>();

let taskForm = ref();

onMounted(() => {
  if (props.action === ACTIONS.UPDATE_TASK) {
    taskForm.value.setFieldValue("name", props.task?.name);
    taskForm.value.setFieldValue("description", props.task?.description);
  }
});

function onSubmit(values: any) {
  if (props.action === ACTIONS.ADD_TASK) {
    kanbanStore.addTaskToColumn(props.columnId, {
      name: values.name,
      description: values.description,
    });
  } else if (props.action === ACTIONS.UPDATE_TASK && props.task?.taskId) {
    let updatedTask = {
      taskId: props.task?.taskId,
      name: values.name,
      description: values.description,
    };
    kanbanStore.updateTask(props.columnId, updatedTask);
  }

  emit("close-modal");
}
</script>
