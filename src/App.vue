<template>
  <div
    class="board"
    v-for="(container, index) in containers"
    :key="index"
    @dragover.prevent="onDragOver($event, index)"
    ref="containerRefs"
  >
    <p
      class="draggable"
      v-for="(item, idx) in container.items"
      :key="idx"
      draggable="true"
      @dragstart="onDragStart($event, index, idx)"
      @dragend="onDragEnd"
    >
      {{ item }}
    </p>
  </div>
</template>

<script setup>
import { ref } from "vue";

const containers = ref([{ items: [1, 2] }, { items: [3, 4] }]);
const containerRefs = ref([]);
const draggingItem = ref(null);

const onDragStart = (event, containerIdx, itemIdx) => {
  draggingItem.value = event.target;
  event.target.classList.add("dragging");
};

const onDragEnd = (event) => {
  event.target.classList.remove("dragging");
  draggingItem.value = null;
};

const onDragOver = (event, containerIndex) => {
  const container = containerRefs.value[containerIndex];
  const afterElement = getDragAfterElement(container, event.clientY);
  const draggable = draggingItem.value;
  if (afterElement == null) {
    container.appendChild(draggable);
  } else {
    container.insertBefore(draggable, afterElement);
  }
};

const getDragAfterElement = (container, y) => {
  // Get all draggable elements except the one being dragged
  const draggableElements = [
    ...container.querySelectorAll(".draggable:not(.dragging)"),
  ];

  return draggableElements.reduce(
    (closest, child) => {
      const box = child.getBoundingClientRect();
      const offset = y - box.top - box.height / 2;
      if (offset < 0 && offset > closest.offset) {
        return { offset: offset, element: child };
      } else {
        return closest;
      }
    },
    { offset: Number.NEGATIVE_INFINITY, element: null }
  ).element;
};

</script>

<style scoped>
body {
  margin: 0;
}

.board {
  background-color: #333;
  padding: 1rem;
  margin-top: 1rem;
}

.draggable {
  padding: 1rem;
  background-color: white;
  border: 1px solid black;
  cursor: move;
}

.draggable.dragging {
  opacity: 0.5;
}

.side-links {
  position: absolute;
  top: 15px;
  right: 15px;
}

.side-link {
  display: flex;
  align-items: center;
  justify-content: center;
  text-decoration: none;
  margin-bottom: 10px;
  color: white;
  width: 180px;
  padding: 10px 0;
  border-radius: 10px;
}

.side-link-youtube {
  background-color: red;
}

.side-link-twitter {
  background-color: #1da1f2;
}

.side-link-github {
  background-color: #6e5494;
}

.side-link-text {
  margin-left: 10px;
  font-size: 18px;
}

.side-link-icon {
  color: white;
  font-size: 30px;
}
</style>
