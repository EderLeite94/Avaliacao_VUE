<template>
  <div class="container mt-2">
    <b-form>
      <b-form-group
      label="Titulo"
      label-for="subject"
      >
      <b-form-input
        id="subject"
        v-model.trim="$v.form.subject.$model"
        type="text"
        placeholder="Titulo"
        required
        autocomplete="off"
        :state="getValidation"
        aria-describedby="subject-feedback"
      ></b-form-input>
      <b-form-invalid-feedback id="subject-feedback">Campo obrigatório</b-form-invalid-feedback>
      </b-form-group>

      <b-form-group
      label="Descrição"
      label-for="description"
      >
      <b-form-textarea
        id="description"
        v-model="form.description"
        type="text"
        placeholder="Postar"
        required
        autocomplete="off"
      ></b-form-textarea>
      </b-form-group>
      <b-button type="submit" variant="outline-primary" @click="saveTask" :disabled="!getValidation" v-b-tooltip.hover title="Salvar"><i class="far fa-save"></i></b-button>
    </b-form>
  </div>
</template>
<script>
  import ToastMixin from "@/mixins/toastMixin.js";
  import TasksModel from "/models/TasksModel";
  import{requered, minLength} from "vuelidate/lib/validators";
  export default{
    name: "Form",
    
    mixins:[ToastMixin],

    data(){
      return{
        form: {
          subject: "",
          description: "",
          userId: JSON.parse(localStorage.getItem('authUser')).id
          
        },
        methodSave: "new"
      }
    },

    validations: {
      form: {
        subject: {
          minLength: minLength(4)
        }
      }
    },

    async created(){
      if(this.$route.params.taskid){
        this.methodSave = "update";
        this.form = await TasksModel.find(this.$route.params.taskid);
        
      }
    },

    methods:{
      saveTask() {
        if(this.methodSave === "update"){
          this.form.save();

        
          this.showToast("success", "Sucesso!", "Atualizado com sucesso");
          this.$router.push({ name: "list" });
          return;
        }

        let tasks = (localStorage.getItem("tasks")) ? JSON.parse(localStorage.getItem("tasks")) :[];
        tasks.push(this.form);
        localStorage.setItem("tasks", JSON.stringify(tasks));

        const task = new TasksModel(this.form);
        task.save();

        this.showToast("success", "Sucesso!", "Criado com sucesso");
        this.$router.push({ name: "list" });
      }
    },
    computed:{
      getValidation(){
        if(this.$v.form.subject.$dirty === false){
          return null;
        }
        return !this.$v.form.subject.$error;
      }
    }
  }

</script>

