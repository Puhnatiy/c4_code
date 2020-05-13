<template>
  <div class="container">
    <div class="col-sm-10">
      <h1>Задачи</h1>
<confirmation :showDismissibleAlert="showDismissibleAlert" :message="message"
v-if="showConfirmation"></confirmation>
      <button type="button" id="task-add" class="btn btn-success btn-sm align-left d-block"
 v-b-modal.todo-modal>Добавить задачу</button>
 <h5>
   Выполненные задачи: {{ task_compl }}, не выполненные задачи: {{ task_no_compl }}
</h5>

      <table class="table table-dark table-stripped table-hover">
        <thead class="thead-light">
          <tr>
            <th>Uid</th>
            <th>Описание</th>
            <th>Выполнена?</th>
            <th></th>
          </tr>
        </thead>

        <tbody>
          <tr v-for="(todo, index) in todos" :key="index">
            <td class="todo-uid">{{ index + 1 }} </td>
            <td>{{ todo.description }}</td>
            <td>
              <span v-if="todo.is_completed">Выполнено</span>
              <span v-else>Не выполнено</span>
            </td>
            <td>
              <div class="btn-group" role="group">
                <button type="button" class="btn btn-secondary btn-sm" v-b-modal.todo-update-modal
 @click="updateTodo(todo)">Обновить</button>
                &nbsp;
                <button type="button" class="btn btn-danger btn-sm" @click="deleteTodo(todo)">X
</button>
              </div>
            </td>
          </tr>
        </tbody>

      </table>
<b-modal ref="addTodoModal"
         id="todo-modal"
         title="Добавить задачу"
         hide-footer>
  <b-form @submit="onSubmit" @reset="onReset" class="w-100">
  <b-form-group id="form-description-group"
                label="Описание:"
                label-for="form-description-input">
    <b-form-input id="form-description-input"
                  type="text"
                  v-model="addTodoForm.description"
                  required
                  placeholder="Завести задачу">
    </b-form-input>
  </b-form-group>
  <b-form-group id="form-complete-group">
    <b-form-checkbox-group v-model="addTodoForm.is_completed" id="form-checks">
      <b-form-checkbox value="true">Задача выполнена?</b-form-checkbox>
    </b-form-checkbox-group>
    </b-form-group>
    <b-button type="submit" variant="primary">Добавить</b-button>
    <b-button type="reset" variant="danger">Сброс</b-button>
  </b-form>
</b-modal>
<b-modal ref="updateTodoModal"
         id="todo-update-modal"
         title="Update"
         hide-footer>
  <b-form @submit="onUpdateSubmit" @reset="onUpdateReset" class="w-100">
  <b-form-group id="form-update-description-group"
                label="Описание:"
                label-for="form-update-description-input">
    <b-form-input id="form-update-description-input"
                  type="text"
                  v-model="updateTodoForm.description"
                  required>
    </b-form-input>
  </b-form-group>
  <b-form-group id="form-update-complete-group">
    <b-form-checkbox-group v-model="updateTodoForm.is_completed" id="form-update-checks">
      <b-form-checkbox value="true">Задача выполнена?</b-form-checkbox>
    </b-form-checkbox-group>
  </b-form-group>
  <b-button-group>
    <b-button type="submit" variant="primary">Обновить</b-button>
    <b-button type="reset" variant="danger">Сброс</b-button>
  </b-button-group>
  </b-form>
</b-modal>
    </div>
  </div>
</template>

<style>
button#task-add {
  margin-top: 20px;
  margin-bottom: 20px;
}
h1, td {
  text-align: left;
}
.todo-uid {
  text-align: right;
}
</style>

<script>
/* eslint linebreak-style: ["error", "windows"] */

import Confirmation from './Confirmation.vue';


export default {
  name: 'Todo',
  data() {
    return {
      todos: [],
      task_compl: 0,
      task_no_compl: 0,
      global_new: 0,
      todos1: [],
      addTodoForm: {
        description: '',
        is_completed: [],
      },
      updateTodoForm: {
        uid: 0,
        description: '',
        is_completed: [],
      },
      message: '',
      showDismissibleAlert: false,
      showConfirmation: false,
    };
  },
  methods: {
    getTodos() {
      this.todos = [];
      localStorage.removeItem('loglevel:webpack-dev-server');
      let keys = Object.keys(localStorage);
      // console.log(keys);
      // console.log('44444');
      this.task_compl = 0;
      this.task_no_compl = 0;
      keys.forEach((item, i) => {
        const uu = JSON.parse(localStorage.getItem(item));
        // console.log(item, localStorage.getItem(item));
        this.todos[i] = uu;
        if (uu.is_completed) {
          this.task_compl += 1;
        } else {
          this.task_no_compl += 1;
        }
        // console.log('задачи выполненные и нет', this.task_compl, this.task_no_compl);
      });
      keys = Object.keys(localStorage);
      keys.forEach((item, i) => {
        const uu = JSON.parse(localStorage.getItem(item));
        // console.log(item, localStorage.getItem(item));
        this.todos[i] = uu;
      });
      // console.log('контроль', this.todos);
    },
    resetForm() {
      this.addTodoForm.description = '';
      this.addTodoForm.is_completed = [];
      this.updateTodoForm.description = '';
      this.updateTodoForm.is_completed = [];
    },
    onSubmit(event) {
      event.preventDefault();
      this.$refs.addTodoModal.hide();
      let isc = this.addTodoForm.is_completed[0];
      if (isc) {
        console.log('pravda');
      } else {
        // console.log('lozh');
        isc = false;
      }
      // console.log('isc', isc);
      localStorage.removeItem('loglevel:webpack-dev-server');
      const keysforadd = Object.keys(localStorage).sort((a, b) => a - b);
      // console.log('keysforadd', keysforadd);
      let newid = Number(keysforadd[keysforadd.length - 1]) + 1;
      if (Number.isInteger(newid)) {
        console.log('проверка на число', newid);
      } else {
        // console.log('элс-проверка на число, newid');
        newid = 1;
      }
      // console.log('newid', newid);
      const requestData = {
        description: this.addTodoForm.description,
        is_completed: isc,
        uid: newid,
      };
      // console.log(requestData);
      // console.log('strochka', JSON.stringify(requestData));
      localStorage.setItem(newid, JSON.stringify(requestData));
      newid += 1;
      this.getTodos();
      this.message = `Задача "${requestData.description}" добавлена`;
      this.showConfirmation = true;
      this.showDismissibleAlert = true;
      this.resetForm();
    },
    onReset(event) {
      event.preventDefault();
      this.$refs.addTodoModal.hide();
      this.resetForm();
    },
    updateTodo(todo) {
      this.updateTodoForm = todo;
    },
    onUpdateSubmit(event) {
      event.preventDefault();
      this.$refs.updateTodoModal.hide();
      let isc1 = this.updateTodoForm.is_completed[0];
      if (isc1) {
        console.log('pravda');
      } else {
        // console.log('lozh');
        isc1 = false;
      }
      const requestData = {
        description: this.updateTodoForm.description,
        is_completed: isc1,
        uid: this.updateTodoForm.uid,
      };
      // проверяем передаваемый id задачи
      // console.log('выполнена?', requestData.is_completed[0]);
      const keysforup = Object.keys(localStorage).sort((a, b) => a - b);
      // console.log('keysforup', keysforup, requestData.uid);
      // console.log('содержит', keysforup.includes(String(requestData.uid)));
      if (keysforup.includes(String(requestData.uid))) {
        localStorage.setItem(requestData.uid, JSON.stringify(requestData));
        this.message = 'Задача обновлена';
      } else {
        this.message = 'Непрвильный id задачи. Задача не обновлена';
      }
      this.getTodos();
      localStorage.removeItem('loglevel:webpack-dev-server');
      this.showConfirmation = true;
      this.showDismissibleAlert = true;
    },
    onUpdateReset(event) {
      event.preventDefault();
      this.$refs.updateTodoModal.hide();
      this.resetForm();
    },
    deleteTodo(todo) {
      localStorage.removeItem('loglevel:webpack-dev-server');
      // проверяем передаваемый id задачи
      // console.log('id_del', todo.uid);
      const keysfordel = Object.keys(localStorage).sort((a, b) => a - b);
      // console.log('keysfordel', keysfordel, todo.uid);
      // console.log('содержит', keysfordel.includes(String(todo.uid)));
      if (keysfordel.includes(String(todo.uid))) {
        localStorage.removeItem(todo.uid);
        this.message = 'Задача удалена из списка';
      } else {
        this.message = 'Непрвильный id задачи. Задача не удалена';
      }
      this.getTodos();
      this.showConfirmation = true;
      this.showDismissibleAlert = true;
      this.resetForm();
    },
  },
  components: {
    confirmation: Confirmation,
  },
  created() {
    this.getTodos();
    localStorage.removeItem('loglevel:webpack-dev-server');
  },
};
</script>\n
