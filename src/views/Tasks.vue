<template>
  <div>
    <h1>liste de tâches</h1>

    <el-row :gutter="12">
      <el-col :span="24"  v-for="task in tasks.data" :key="task.id">
        <el-card class="box-card" shadow="hover" style="margin: 5px 0">
          <div class="clearfix">
            <el-button style="float: right; margin-top: -5px" @click="deleteTask(task.id)" type="success" icon="el-icon-check" circle></el-button>
            <div>{{task.title}}</div>
            <div>{{task.description}}</div>
          </div>
        </el-card>
      </el-col>
    </el-row>
    <!--Ajouter à partir d'ici-->
    <el-row style="margin: 10px 0">
      <el-col :span="10" >
        <el-input placeholder="title" v-model="title" @submit="onSubmit"></el-input>
      </el-col>
      <el-col :span="20">
        <el-input placeholder="description" v-model="description" @submit="onSubmit"></el-input>
      </el-col>
    </el-row>
    <el-row style="margin: 10px 0">
      <el-button type="primary" @click="onSubmit">Créer</el-button>
    </el-row>
    <!--Ajouter ici-->
    <el-button type="success" @click="getTasks()" v-if="showGetTasksButton">task取得</el-button>
    <!--Omis ci-dessous.-->
    
  </div>
</template>

<script>
import axios from 'axios'

const HTTP = axios.create({
  headers: {
    'Accept': 'application/json',
    'Content-Type':'application/json',
  },
})
const URL_DATA = 'http://localhost:3000/api/v1/tasks/'

export default {
  name: 'Tasks',
  data () {
    return {
      tasks: [],
      title: '', // Propriété pour stocker la valeur saisie dans le champ title
      description: '',   // Propriété pour stocker la valeur saisie dans le champ description
      showGetTasksButton: true // Ajoutez une variable de données pour contrôler l'affichage du bouton

    }
  },
  methods: {
    getTasks() {

      this.showGetTasksButton = false;
      return HTTP.get(URL_DATA,
        {
          status: 'open',
        },
      )
      .then((res) => {
        this.tasks = res.data
      })
      .catch((error) => {
        // En cas d'erreur, afficher à nouveau le bouton
        this.showGetTasksButton = true;
        console.error(error);
      });
    },

    onSubmit(){
      HTTP.post(URL_DATA,{
        title: this.title,
        description: this.description 
      })
        .then((res) => {
          if (res.status === 200) {
            //Inscription terminée
          } else {
            throw new Error('レスポンスエラー')
          }
          this.title="";
          this.description="";

      })
     
    },
    deleteTask(index){
      HTTP.delete(URL_DATA + index)
      .then((res) => {
        if (res.status === 200) {
          //Suppression complète.
        } else {
          throw new Error('レスポンスエラー')
        }        
        this.getTasks();
      })
    }    
  },

  mounted() {
    // Appel de la méthode getTasks() lorsque le composant est inséré dans le DOM pour la première fois
    this.getTasks();
  },

  beforeUpdate() {
    // Appel de la méthode getTasks() lorsque le composant est inséré dans le DOM pour la première fois
    this.getTasks();
  }
}
</script>