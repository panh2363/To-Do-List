<template>
  <div id="app">
    <div class="container">
      <h1>Danh Sách Công Việc</h1>
      <!-- Thanh thêm công việc -->
      <div class="input-container">
        <input
          v-model="newTask"
          placeholder="Nhập công việc mới..."
          @keyup.enter="addTask"
        />
        <button @click="addTask">Thêm Công Việc</button>
        <button @click="downloadTasksAsJSON">Lưu</button>
      </div>

      <div v-if="showSuccessDialog" class="success-dialog">
        <p>File đã được lưu thành công!</p>
        <button @click="closeDialog">Đóng</button>
      </div>

      <!-- Danh sách công việc -->
      <ol class="olcards">
        <li
          v-for="(task, index) in tasks"
          :key="index"
          :style="{ '--cardColor': getCardColor(index) }"
        >
          <div class="content">
           
            <!-- Tiêu đề công việc, áp dụng hiệu ứng gạch ngang nếu đã hoàn thành -->
            <div class="title" :class="{ completed: task.completed }">
              {{ task.title }}
            </div>
            
            <!-- Icon hoàn thành -->
            <div class="icon" @click="toggleCompletion(index)">
              {{ task.completed ? '😆' : '😭' }}
            </div>
          </div>
          
          <!-- Nút xóa và sửa -->
          <div class="actions">
            <button class="edit" @click="editTask(index)">Sửa</button>
            <button class="delete" @click="deleteTask(index)">Xóa</button>
          </div>
        </li>
      </ol>
    </div>
  </div>

</template>


<script>
export default {
  name: "App",
  data() {
    return {
      newTask: "",
      tasks: [],
      fileHandle: null, // Lưu trữ file được chọn
      colors: ["#fc374e", "#36aeb3", "#162d59", "#f15f0e"],
      showSuccessDialog: false, // Biến kiểm soát hiển thị hộp thoại thành công
    };
  },
  methods: {
    addTask() {
      if (this.newTask.trim()) {
        this.tasks.push({ title: this.newTask, completed: false });
        this.newTask = "";
      }
    },
    deleteTask(index) {
      this.tasks.splice(index, 1);
    },
    editTask(index) {
      const updatedTask = prompt("Nhập công việc mới:", this.tasks[index].title);
      if (updatedTask !== null && updatedTask.trim() !== "") {
        this.tasks[index].title = updatedTask;
      }
    },
    toggleCompletion(index) {
      this.tasks[index].completed = !this.tasks[index].completed;
    },
    getCardColor(index) {
      return this.colors[index % this.colors.length];
    },
    async downloadTasksAsJSON() {
      const jsonData = JSON.stringify(this.tasks, null, 2);

      try {
        if (!this.fileHandle) {
          this.fileHandle = await window.showSaveFilePicker({
            suggestedName: "tasks.json",
            types: [
              {
                description: "JSON File",
                accept: { "application/json": [".json"] },
              },
            ],
          });
        }

        const writable = await this.fileHandle.createWritable();
        await writable.write(jsonData);
        await writable.close();
        this.showSuccessDialog = true; // Hiển thị hộp thoại thành công
      } catch (err) {
        console.error("Lỗi khi lưu file:", err);
        alert("Không thể lưu file. Vui lòng thử lại.");
      }
    },
    closeDialog() {
      this.showSuccessDialog = false; // Đóng hộp thoại
    },
  },
};
</script>


<style scoped>
body {
  background: #d8d9dc;
  padding: 2rem;
}
h1 {
  font-family: sans-serif;
}
.olcards,
.olcards * {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.olcards {
  list-style: none;
  counter-reset: cardCount;
  font-family: sans-serif;
  display: flex;
  flex-direction: column;
  --cardsGap: 1rem;
  gap: var(--cardsGap);
  padding-bottom: var(--cardsGap);
}

.olcards li {
  counter-increment: cardCount;
  display: flex;
  color: rgb(243, 243, 246);
  --labelOffset: 1rem;
  --arrowClipSize: 1.5rem;
  margin-top: var(--labelOffset);
}

.olcards li::before {
  content: counter(cardCount, decimal-leading-zero);
  background: rgb(245, 246, 247);
  color: var(--cardColor);
  font-size: 2em;
  font-weight: 700;
  transform: translateY(calc(-1 * var(--labelOffset)));
  margin-right: calc(-1 * var(--labelOffset));
  z-index: 1;
  display: flex;
  justify-content: center;
  align-items: center;
  padding-inline: 0.5em;
}

.olcards li .content {
  background-color: var(--cardColor);
  --inlinePadding: 1em;
  --boxPadding: 0.5em;
  display: grid;
  padding: var(--boxPadding) calc(var(--inlinePadding) + var(--arrowClipSize))
    var(--boxPadding) calc(var(--inlinePadding) + var(--labelOffset));
  grid-template-areas:
    "icon title"
    "icon text";
  gap: 0.25em 1em;
  clip-path: polygon(
    0 0,
    calc(100% - var(--arrowClipSize)) 0,
    100% 50%,
    calc(100% - var(--arrowClipSize)) 100%,
    calc(100% - var(--arrowClipSize)) calc(100% + var(--cardsGap)),
    0 calc(100% + var(--cardsGap))
  );
  position: relative;
}
.olcards li .content::before {
  content: "";
  position: absolute;
  width: var(--labelOffset);
  height: var(--labelOffset);
  background: var(--cardColor);
  left: 0;
  bottom: 0;
  clip-path: polygon(0 0, 100% 0, 0 100%);
  filter: brightness(0.75);
}
.olcards li .content::after {
  content: "";
  position: absolute;
  height: var(--cardsGap);
  width: var(--cardsGap);
  background: linear-gradient(to right, rgba(0, 0, 0, 0.25), transparent 50%);
  left: 0;
  top: 100%;
}

.olcards li .icon {
  grid-area: icon;
  align-self: center;
  font-size: 2em;
  cursor: pointer;
}

.olcards li .content .title {
  grid-area: title;
  font-size: 1.25em;
  align-self: center;
  margin-top: 0.4em;
}

.olcards li .content .text {
  grid-area: text;
  
}

.olcards li .actions {
  display: flex;
  gap: 1rem;
  justify-content: flex-start;
  margin-top: 0.5rem;
  margin-left: 1rem; /* Tạo khoảng cách giữa nút và thanh công việc */
}

.olcards li .actions button {
  background-color: #f1f1f1;
  border: none;
  color: #333;
  font-size: 1em;
  padding: 0.5em 1em;
  border-radius: 8px;
  cursor: pointer;
  transition: background-color 0.3s ease, transform 0.2s ease;
}

.olcards li .actions button:hover {
  background-color: #36aeb3;
  color: white;
  transform: translateY(-2px);
}

.olcards li .actions button.edit {
  background-color: #c8dcf0;
  color: rgb(31, 31, 31);
}

.olcards li .actions button.delete {
  background-color: #c8dcf0;
  color: rgb(31, 31, 31);
}


.olcards li .content .title.completed {
  text-decoration: line-through; /* Thêm gạch ngang khi công việc hoàn thành */
  color: rgba(0, 0, 0, 0.5); /* Làm mờ màu chữ khi công việc hoàn thành */
}

.input-container button {
  margin-left: 0.5rem;
  padding: 0.5rem 1rem;
  font-size: 1rem;
  border: none;
  border-radius: 8px;
  background-color: #36aeb3;
  color: white;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.input-container button:hover {
  background-color: #2b8e96;
}

.success-dialog {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: white;
  padding: 2rem;
  border: 1px solid #ccc;
  border-radius: 8px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  z-index: 1000;
  text-align: center;
}

.success-dialog button {
  margin-top: 1rem;
  padding: 0.5rem 1rem;
  font-size: 1rem;
  background-color: #36aeb3;
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.success-dialog button:hover {
  background-color: #2b8e96;
}


</style>
