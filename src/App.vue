<template>
  <div class="container mt-5 display word-wrapped">
    <h1>Kanban Board 2020</h1>
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
            <div @mouseenter="element.status = 'toDo'"
              class="list-group-item"
              v-for="element in toDo"
              :key="element.content"
            >
              <h5>Task #{{ element.header }}</h5>
              {{ element.content }}
              <br><br>
              <h6>Doer</h6>
              {{ element.doer }}
              <br><br>
              <b-button class="btn-sm" @click="editItem(element)"> <b-icon-gear-fill></b-icon-gear-fill> </b-button>

              <b-button class="btn-sm" @click="deleteItem('toDo', element)"> <b-icon-x-circle-fill></b-icon-x-circle-fill> </b-button>
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
            <div @mouseenter="element.status = 'inProgress'"
              class="list-group-item"
              v-for="element in inProgress"
              :key="element.content"
            >
              <h5>Task #{{ element.header }}</h5>
              {{ element.content }}
              <br><br>
              <h6> Start time </h6>
              {{ element.start | datetimeFilter}}
              <br><br>
              <h6>Doer</h6>
              {{ element.doer }}
              <br><br>
              <b-button class="btn-sm" @click="editItem(element)"> <b-icon-gear-fill></b-icon-gear-fill> </b-button>
              <b-button class="btn-sm" @click="deleteItem('inProgress', element)"> <b-icon-x-circle-fill></b-icon-x-circle-fill> </b-button>
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
            <div @mouseenter="element.status = 'done'"
              class="list-group-item"
              v-for="element in done"
              :key="element.content"
            >
              <h5>Task #{{ element.header }}</h5>
              {{ element.content }}
              <br><br>
              <h6> Start time </h6>
              {{ element.start | datetimeFilter }}
              <br><br>
              <h6> End time </h6>
              {{ element.end | datetimeFilter }}
              <br><br>
              <h6>Doer</h6>
              {{ element.doer }}
              <br><br>
              <b-button class="btn-sm" @click="editItem(element)"> <b-icon-gear-fill></b-icon-gear-fill> </b-button>
              <b-button class="btn-sm" @click="deleteItem('done', element)"> <b-icon-x-circle-fill></b-icon-x-circle-fill> </b-button>
            </div>
          </draggable>
        </div>
      </div>
    </div>
    <b-modal id="editModal" title="Edit item" @ok="editItemSubmit()">
      <form>
        <div class="modal-body">
          <div class="form-group">
              <label>Description</label>
              <b-form-input type="text" class="form-control"
              v-model="modalContent" :value="modalContent">
              </b-form-input>
          </div>
          <div class="form-group">
              <label>Status</label>
              <b-form-select class="form-control" v-model="modalStatus">
                <option value="toDo">ToDo</option>
                <option value="inProgress">In Progress</option>
                <option value="done">Done</option>
              </b-form-select>
          </div>
          <div class="form-group">
              <label>Doer</label>
              <b-form-input type="text" class="form-control"
                            v-model="modalDoer" :value="modalDoer">
              </b-form-input>
          </div>
          <div class="form-group">
              <label>Start time</label>
              <b-form-input type="text" class="form-control"
                            v-model="modalStartDate"></b-form-input>
              <b-form-input type="text" class="form-control"
                            v-model="modalStartTime"></b-form-input>
          </div>
          <div class="form-group">
              <label>End time</label>
              <b-form-input type="text" class="form-control"
                            v-model="modalEndDate"></b-form-input>
              <b-form-input type="text" class="form-control"
                            v-model="modalEndTime"></b-form-input>
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
    this.selectedItem = null;
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
        modalEndTime: "",
        selectedItem: this.selectedItem
    }
  },
  filters: {
    datetimeFilter: d => {
      d = new Date(d);
      return [
      [
        `0${d.getDate()}`.slice(-2),
        `0${d.getMonth() + 1}`.slice(-2),
        d.getFullYear(),
      ].join('.'),
      [
        `0${d.getHours()}`.slice(-2),
        `0${d.getMinutes()}`.slice(-2),
      ].join(':')
    ].join(' ')},
    dateFilter: d =>{
      d = new Date(d);
      return [
        d.getFullYear(),
        `0${d.getMonth() + 1}`.slice(-2),
        `0${d.getDate()}`.slice(-2),
      ].join('-')},
    timeFilter: d => {
      d = new Date(d);
      return [
        `0${d.getHours()}`.slice(-2),
        `0${d.getMinutes()}`.slice(-2),
      ].join(':')}
  },
  methods: {
    datetimeGetter: d => {
      d = new Date(d);
      return [
      [
        `0${d.getDate()}`.slice(-2),
        `0${d.getMonth() + 1}`.slice(-2),
        d.getFullYear(),
      ].join('.'),
      [
        `0${d.getHours()}`.slice(-2),
        `0${d.getMinutes()}`.slice(-2),
      ].join(':')
    ].join(' ')},
    dateGetter: d =>{
      d = new Date(d);
      return [
        d.getFullYear(),
        `0${d.getMonth() + 1}`.slice(-2),
        `0${d.getDate()}`.slice(-2),
      ].join('-')},
    timeGetter: d => {
      d = new Date(d);
      return [
        `0${d.getHours()}`.slice(-2),
        `0${d.getMinutes()}`.slice(-2),
      ].join(':')},
    elementChangeEvent: function(){
      localStorage.setItem('itemsLog',JSON.stringify(
        [ this.toDo, this.inProgress, this.done ]));
        },
    add: function() {
      if (this.newTask) {
        let lastHeader = +localStorage.getItem('lastHeader');
        lastHeader += 1;
        localStorage.setItem('lastHeader',lastHeader);
        this.toDo.push({ header: lastHeader,
        content: this.newTask, doer: "", start: new Date().toJSON(), end: new Date().toJSON()  , status: "toDo"});
        this.newTask = "";
        this.elementChangeEvent();
      }
    },
    deleteItem: function(list, element){
      this[list].splice(this.done.find((el)=>(el.header===element.header)), 1);
      this.elementChangeEvent();
    },
    editItem: function(element){
      this.selectedItem = element;

      this.modalContent = element.content;
      this.modalStatus = element.status;
      this.modalDoer = element.doer;
      this.modalStartDate = this.dateGetter(element.start);
      this.modalStartTime = this.timeGetter(element.start);
      this.modalEndDate =  this.dateGetter(element.end);
      this.modalEndTime =  this.timeGetter(element.end);

      this.$bvModal.show('editModal')
    },
    test: (d) => console.log(d),
    editItemSubmit: function(){
      this.selectedItem.content = this.modalContent;

      this.selectedItem.doer = this.modalDoer;

      this.selectedItem.start = this.modalStartDate+' '+this.modalStartTime;
      this.selectedItem.end = this.modalEndDate+' '+this.modalEndTime;

      this[this.modalStatus].push(JSON.parse(JSON.stringify(this.selectedItem)));
      this.deleteItem(this.selectedItem.status, this.selectedItem);

      this.selectedItem.status = this.modalStatus;

      this.elementChangeEvent();
    }
  }
};


</script>

<style>
.kanban-column {
  min-height: 300px;
}
.word-wrapped {
  word-wrap: break-word;
}
.btn-sm + .btn-sm {
  margin-left: 10px;
}
</style>
