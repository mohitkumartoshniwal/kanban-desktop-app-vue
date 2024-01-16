<template>
  <div
    draggable="true"
    @dragstart.self="onDrag"
    @dragover.prevent
    @dragenter.prevent
  >
    <slot />
  </div>
</template>
<script setup lang="ts">
import type { TRANSFER_DATA } from "../../types";
import type { PropType } from "vue";

const props = defineProps({
  transferData: {
    type: Object as PropType<TRANSFER_DATA>,
    required: true,
  },
});
// const emit = defineEmits<{
//   (e: "drop", payload: TRANSFER_DATA): void;
// }>();
function onDrag(e: DragEvent) {
  if (e.dataTransfer) {
    e.dataTransfer.effectAllowed = "move";
    e.dataTransfer.dropEffect = "move";

    e.dataTransfer.setData("payload", JSON.stringify(props.transferData));

    // emit("drop", props.transferData);
  }
}
</script>
