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
      <div class="col-4">
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

      <div class="col-4">
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
              <b-button class="btn-sm" @click="editItem(element)"> <b-icon-gear-fill></b-icon-gear-fill> </b-button>
            </div>
          </draggable>
        </div>
      </div>

      <div class="col-4">
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
              <b-button class="btn-sm" @click="editItem(element)"> <b-icon-gear-fill></b-icon-gear-fill> </b-button>
            </div>
          </draggable>
        </div>
      </div>
    </div>
    <b-modal id="editModal" title="Edit item">
      <form>
        <div class="modal-body">
          <div class="form-group">
              <label>Description</label>
              <b-form-input type="text" class="form-control" v-model="modalContent" :value="modalContent">
              </b-form-input>
          </div>
          <div class="form-group">
              <label>Status</label>
              <b-form-select class="form-control">

              </b-form-select>
          </div>
          <div class="form-group">
              <label>Doer</label>
              <b-form-input type="text" class="form-control">
              </b-form-input>
          </div>
          <div class="form-group">
              <label>Start time</label>
              <b-form-input type="date" class="form-control"></b-form-input>
              <b-form-input type="time" class="form-control"></b-form-input>
          </div>
          <div class="form-group">
              <label>End time</label>
              <b-form-input type="date" class="form-control"></b-form-input>
              <b-form-input type="time" class="form-control"></b-form-input>
          </div>
        </div>
      </form>
    </b-modal>
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
        this.toDo = itemsLog[0];
        this.inProgress = itemsLog[1];
        this.done = itemsLog[2];
    }
    else {
      this.toDo = [];
      this.inProgress = [];
      this.done = [];
    }
    return {
        toDo: this.toDo,
        inProgress: this.inProgress,
        done: this.done,
        newTask: "",
        modalContent: "",
        modalStatus: "",
        modalDoer: "",
        modalStartDate: "",
        modalStartTime: "",
        modalEndDate: "",
        modalEndTime: ""
    }
  },
  methods: {
    save: function(){
      localStorage.setItem('itemsLog',JSON.stringify(
        [ this.toDo, this.inProgress, this.done ]));
        },
    add: function() {
      if (this.newTask) {
        let lastHeader = +localStorage.getItem('lastHeader');
        lastHeader += 1;
        localStorage.setItem('lastHeader',lastHeader);
        this.toDo.push({ header: lastHeader,
        content: this.newTask, doer: "", start: new Date(), end: new Date(), status: "toDo"});
        this.newTask = "";
        this.save();
      }
    },
    editItem: function(element){
      this.selectedItem = element;
      this.modalContent = element.content;
      this.modalStatus = element.status;
      this.modalDoer = element.doer;

      this.$bvModal.show('editModal')
    }
  }
};


</script>

<style>
.kanban-column {
  min-height: 300px;
}
</style>
