<template>
  <Form
    ref="columnForm"
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
    <button type="submit">Submit</button>
  </Form>
</template>
<script setup lang="ts">
import { Field, Form, ErrorMessage } from "vee-validate";
import { toTypedSchema } from "@vee-validate/zod";

import { columnFormSchema } from "../schemas";
import STORE from "../stores/kanbanStore";
import { ACTIONS, type Column } from "../types";
import { onMounted, ref } from "vue";

const validationSchema = toTypedSchema(columnFormSchema);

const emit = defineEmits<{
  (e: "close-modal"): void;
}>();

const props = defineProps<{
  column?: Column;
  action: ACTIONS;
}>();

let columnForm = ref(null);

onMounted(() => {
  if (props.action === ACTIONS.UPDATE_COLUMN) {
    (columnForm.value as any).setFieldValue("name", props.column?.name);
  }
});

function onSubmit(values: any) {
  if (props.action === ACTIONS.ADD_COLUMN) {
    STORE.addColumn(values.name);
  } else if (props.action === ACTIONS.UPDATE_COLUMN && props.column?.columnId) {
    STORE.updateColumn({
      name: values.name,
      columnId: props.column?.columnId,
    });
  }
  emit("close-modal");
}
</script>
