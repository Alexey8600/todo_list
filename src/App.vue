<template>
  <div class="text">
    <h1>Todo List</h1>
  </div>
  <div class="text2">
    <h2>You`ve got {{ notes.length }} task today</h2>
    <button class="btn" @click="openModal()">+ Add New</button>
  </div>

  <!-- Модальное окно -->
  <div v-if="showModal" class="modal-overlay">
    <div class="modal">
      <h2>{{ isEditing ? "Edit Note" : "Add Note" }}</h2>
      <form class="form" @submit.prevent="saveNote">
        <input
          class="input"
          type="text"
          placeholder="Note Name"
          v-model="newNote"
        />
        <div class="buttons-container">
          <button class="btn2" type="submit">
            {{ isEditing ? "Save" : "Add" }}
          </button>
          <button class="btn2" type="button" @click="closeModal">Cancel</button>
        </div>
      </form>
    </div>
  </div>

  <!-- Notes -->
  <div>
    <h2>All Notes</h2>
    <div class="notes">
      <ul>
        <li v-for="(note, index) in notes" :key="index" class="li">
          <span :class="{ completed: note.completed }">{{ note.text }}</span>
          <button
            v-if="!note.completed"
            class="btn3"
            @click="markAsDone(index)"
          >
            <i class="fas fa-check"></i>
          </button>
          <button v-if="!note.completed" class="btn3" @click="openModal(index)">
            <i class="fas fa-edit"></i>
          </button>

          <button class="btn3" @click="deleteNote(index)">
            <i class="fas fa-trash"></i>
          </button>
        </li>
      </ul>
    </div>
  </div>
</template>

<style>
@import "@/assets/app.css";
</style>

<script>
export default {
  data() {
    return {
      showModal: false,
      newNote: "",
      isEditing: false, // редактируется ли заметка
      editingIndex: null,
      notes: [],
    };
  },
  created() {
    this.loadNotes(); // Загружаем заметки при инициализации компонента
  },
  methods: {
    loadNotes() {
      // Загружаем заметки из LocalStorage (если они есть)
      const savedNotes = JSON.parse(localStorage.getItem("notes"));
      if (savedNotes) {
        this.notes = savedNotes;
      }
    },

    saveNotes() {
      // Сохраняем заметки в LocalStorage
      localStorage.setItem("notes", JSON.stringify(this.notes));
    },

    openModal(index = null) {
      if (index !== null) {
        this.isEditing = true;
        this.editingIndex = index;
        this.newNote = this.notes[index].text;
      } else {
        this.isEditing = false;
        this.newNote = "";
      }
      this.showModal = true;
    },

    closeModal() {
      this.showModal = false;
      this.newNote = "";
    },

    // Сохранение или обновление заметки
    saveNote() {
      if (this.newNote.trim()) {
        if (this.isEditing) {
          // редактируем
          this.notes[this.editingIndex].text = this.newNote.trim();
        } else {
          // добовляем
          this.notes.push({ text: this.newNote.trim(), completed: false });
        }
        this.saveNotes(); // Сохраняем заметки в LocalStorage
        this.closeModal();
      }
    },

    markAsDone(index) {
      this.notes[index].completed = true;
      this.saveNotes(); // Сохраняем изменения выполнено
    },

    deleteNote(index) {
      this.notes.splice(index, 1);
      this.saveNotes(); // Сохраняем изменения удалено
    },
  },
};
</script>
