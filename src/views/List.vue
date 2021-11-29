<template>
    <div class="container mt-2">
  <template v-if="isLoading">
    <div class="loading-spin">
      <b-spinner style="width: 5rem height:5rem;"></b-spinner>
    </div>
  </template>
  <template v-if="isTasksEmpty && !isLoading">
    <div class="empty-data mt-2 container">
      <img src="../assets/imagens/empty-post.svg" class="empty-data-image">
      <b-button variant="outline-primary" class="mt-2" size="lg" to="/form">Criar Post</b-button>
    </div>
  </template>
  <template v-if="!isTasksEmpty && !isLoading">   
    <b-row>      
      <div class="col-8">
    <div v-for="task in tasks" :key="task.id">
    <b-card :title="task.subject" class="mb-2">
      <b-card-text>{{ task.description }}</b-card-text>
    <b-button variant="outline-secondary" class="mr-2" @click="edit(task.id)" v-b-tooltip.hover title="Editar"><i class="far fa-edit"></i></b-button>
    <b-button variant="outline-danger" class="mr-2" @click="remove(task.id)" v-b-tooltip.hover title="Remover"><i class="far fa-trash-alt"></i></b-button>
    </b-card>
    </div>
      </div>
  </b-row>
  </template>
      <b-modal ref="modalRemove" hide-footer title="Excluir">
      <div class="d-block text-center">
        Deseja realmente excluir? {{ taskSelected.subject }}
      </div>
      <div class="mt-3 d-flex justify-content-end">
        <b-button variant="outline-secondary" class="mr-2" @click="hideModal" ><i class="far fa-window-close"></i></b-button>
        <b-button variant="outline-danger" class="mr-2" @click="confirmRemoveTask"><i class="far fa-trash-alt"></i></b-button>
      </div>
    </b-modal>
    
  </div>
  
</template>

<script>

  import TasksModel from "/models/TasksModel";
  import ToastMixin from "@/mixins/toastMixin.js";
export default {
  name:"List",
  mixins: [ToastMixin],
  data(){
    return{
      tasks:[],
      taskSelected:[],
    }
  },
  async created() {
    this.tasks = await this.getTasks();
  },

  methods: {
    edit(taskId) {
      this.$router.push({ name: "form", params: { taskId } });
    },

    async remove(taskId) {
      this.taskSelected = await TasksModel.find(taskId);
      this.$refs.modalRemove.show();
    },

    hideModal() {
      this.$refs.modalRemove.hide();
    },

    async confirmRemoveTask() {
      this.taskSelected.delete();
      this.tasks = await this.getTasks();
      this.hideModal();
    },
  },
  computed:{
    isTasksEmpty(){
      return this.tasks.length ===0;
    }
  }
  }
</script>
