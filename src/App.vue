<template>
  <div class="container mt-5">
    <div class="row">
      <div class="col form-inline">
        <b-form-input
          id="input"
          v-model="newTask"
          required
          placeholder="Enter Task"
          @keyup.enter="add"
        ></b-form-input>
        <b-button @click="add" variant="primary" class="ml-3">Add</b-button>
      </div>
    </div>
    <div class="row mt-5">
      <div class="col-3">
        <div class="p-2 alert alert-secondary">
          <h3>ToDo</h3>
          <draggable
            class="list-group kanban-column"
            :list="toDo"
            group="tasks"
          >
            <div @dragend="save"
              class="list-group-item"
              v-for="element in toDo"
              :key="element.content"
            >
              <h5>Task #{{ element.header }}</h5>
              {{ element.content }}
            </div>
          </draggable>
        </div>
      </div>

      <div class="col-3">
        <div class="p-2 alert alert-primary">
          <h3>In Progress</h3>
          <draggable
            class="list-group kanban-column"
            :list="inProgress"
            group="tasks"
          >
            <div @dragend="save"
              class="list-group-item"
              v-for="element in inProgress"
              :key="element.content"
            >
              <h5>Task #{{ element.header }}</h5>
              {{ element.content }}
              <h6> Start time </h6>
              {{ element.start }}
              <b-button class="btn-sm" @click="edit(element)"> <b-icon-gear-fill></b-icon-gear-fill> </b-button>
            </div>
          </draggable>
        </div>
      </div>

      <div class="col-3">
        <div class="p-2 alert alert-warning">
          <h3>Verifying</h3>
          <draggable
            class="list-group kanban-column"
            :list="toVerify"
            group="tasks"
          >
            <div @dragend="save"
              class="list-group-item"
              v-for="element in toVerify"
              :key="element.content"
            >
              <h5>Task #{{ element.header }}</h5>
              {{ element.content }}
              <h6> Start time </h6>
              {{ element.start }}
              <b-button class="btn-sm" @click="edit(element)"> <b-icon-gear-fill></b-icon-gear-fill> </b-button>
            </div>
          </draggable>
        </div>
      </div>

      <div class="col-3">
        <div class="p-2 alert alert-success">
          <h3>Done</h3>
          <draggable
            class="list-group kanban-column"
            :list="done"
            group="tasks"
          >
            <div @dragend="save"
              class="list-group-item"
              v-for="element in done"
              :key="element.content"
            >
              <h5>Task #{{ element.header }}</h5>
              {{ element.content }}
              <h6> Start time </h6>
              {{ element.start }}
              <h6> End time </h6>
              {{ element.end}}
              <b-button class="btn-sm" @click="edit(element)"> <b-icon-gear-fill></b-icon-gear-fill> </b-button>
            </div>
          </draggable>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import draggable from "vuedraggable";
import { BIconGearFill } from 'bootstrap-vue';
export default {
  name: "kanban-board",
  components: {
    draggable,
    // eslint-disable-next-line vue/no-unused-components
    BIconGearFill
  },
  data() {
    if (localStorage.getItem('lastHeader'));
    else localStorage.setItem('lastHeader', 0);

    let itemsLog = JSON.parse(localStorage.getItem('itemsLog'));

    if (itemsLog){
      return {
        newTask: "",
        toDo: itemsLog[0],
        inProgress: itemsLog[1],
        toVerify: itemsLog[2],
        done: itemsLog[3]
      }
    } else {
      return {
        newTask: "",
        toDo: [],
        inProgress: [],
        toVerify: [],
        done: []
      }
    }
  },
  methods: {
    add: function() {
      if (this.newTask) {
        let lastHeader = +localStorage.getItem('lastHeader');
        lastHeader += 1;
        localStorage.setItem('lastHeader',lastHeader);
        this.toDo.push({ header: lastHeader,
        content: this.newTask, doer: null, start: null, end: null });
        this.newTask = "";
      }
    },
    save: function(){
      localStorage.setItem('itemsLog',JSON.stringify(
        [ this.toDo, this.inProgress, this.toVerify, this.done ]));
        },
    edit: function(element){
      this.selectedItem = element;
    }
  }
};


</script>

<style>
.kanban-column {
  min-height: 300px;
}
</style>
