<template>
  <div class="container">
    <div class="tareas">
      <div>
        <h2>To Do list</h2>
      </div>
      <div>
        <Button label="Primary" @click="visible = true">Crear tarea</Button>
      </div>

      <!-- Lista de Tareas en tarjetas -->
      <div class="tareas-lista">
        <h3>Tareas Actuales:</h3>
        <ProgressSpinner v-if="this.loading" style="width: 50px; height: 50px" strokeWidth="8" fill="transparent"
          animationDuration=".5s" aria-label="Custom ProgressSpinner" />

        <TaskCard v-for="(tarea, index) in tareas" :key="tarea.nombreTarea" :tarea="tarea"
          @delete="borrarTarea(index)" />
      </div>

      <div class="avatarMove">
        <img :src="`/imgs/avatars/${selectedAvatarStore.avatar}.png`" class="avatar">
      </div>

      <!-- Modal para crear tarea -->
      <TaskCreationModal :visible="visible" :categorias="categorias" @update:visible="visible = $event"
        @create-task="agregarTarea" />

      <!-- Modal de confirmación para eliminar tarea -->
      <ConfirmationModal :visible="confirmVisible" @update:visible="handleVisibilityChange" @confirm="confirmDelete" />
    </div>

    <div class="background-campo">
      <div class="borde-campo"></div>
      <div class="campo">
        <div @click="() => console.log(index)" v-for="(parcela, index) in Array(20)" s :key="index" class="parcela"
          :style="estiloParcela(index)"></div>

        <!-- IDEA TOOLTIP -->
        <!-- :data-parcela="index" para acceder a las parcelas. la ruta PointerEvent.target.dataset -->
      </div>
      <div class="numTareas">
        <!-- <p>Tus tareas: {{ tareas.length }}</p> -->
        <div v-if="error" class="error-container">
          <p class="error-message">{{ error }}</p>
          <button @click="limpiarError" class="ok-button">OK</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import ConfirmationModal from './ConfirmationModal.vue';
import TaskCard from './TaskCard.vue';
import TaskCreationModal from './TaskCreationModal.vue';
import { useSelectedAvatarStore } from "@/stores/selectedAvatar";


const username = "aleh";
const API_URL = `https://node-todos.vercel.app/users/${username}`;

//get method:https://node-todos.vercel.app/users/aleh/todos
//post method:https://node-todos.vercel.app/users/aleh/todos
// https://date-fns.org/ para descargar fechas

export default {
  setup() {
    const selectedAvatarStore = useSelectedAvatarStore()
    return { selectedAvatarStore }
  },
  components: {
    ConfirmationModal,
    TaskCard,
    TaskCreationModal
  },
  data() {
    return {

      //Petiones GET para obtener info de la API
      //https://node-todos.vercel.app/api-docs/#/todos
      loading: false,

      tareas: [

      ],

      visible: false,
      error: '',
      categorias: [
        { name: 'Personal', code: 'cat01' },
        { name: 'Trabajo', code: 'cat02' },
        { name: 'Hogar', code: 'cat03' },
        { name: 'Estudio', code: 'cat04' },
        { name: 'Otro', code: 'cat05' }
      ],
      confirmVisible: false,
      deleteIndex: null,
    };
  },
  methods: {

    setup() {
      const constacsStore = useconcastStore();
      return {
        constacsStore
      }
    },

    async obtenerTareas() {
      this.loading = true
      const promesaFetch = fetch(`https://node-todos.vercel.app/users/pollo/todos`)
      const response = await promesaFetch;
      const promesaJson = response.json()
      const tasks = await promesaJson

      this.loading = false
      this.tareas = tasks
    },


    agregarTarea(task) {
      if (this.tareas.length < 20) {

        // event.preventDefault();
        // fetch(`${API_URL}/todos`)
        fetch(`https://node-todos.vercel.app/users/pollo/todos`, {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify({

            text: task.nombreTarea,
            description: task.categoriaTarea.name,
          }),
        })
          .then((response) => response.json())
          .then((data) => {
            console.log(data);
            this.tareas.push(data);

          })
          .catch((error) => {
            console.log(error);
          });

        this.visible = false;
      } else {
        this.error = 'No puedes crear más tareas. El máximo es 20.';
      }
    },

    borrarTarea(index) {
      this.deleteIndex = index;
      this.confirmVisible = true;
    },
    confirmDelete() {
      this.tareas.splice(this.deleteIndex, 1);
      this.confirmVisible = false;
      this.deleteIndex = null;
    },
    handleVisibilityChange(newValue) {
      this.confirmVisible = newValue;
    },
    estiloParcela(index) {
      const imagenParcela = this.tareas.length > index ? '/imgs/germinada.png' : '/imgs/parcela.png';
      return {
        backgroundImage: `url(${imagenParcela})`,
      };
    },
    checkNumTareas() {
      if (this.tareas.length > 20) {
        this.error = 'No puedes crear más tareas. El máximo es 20.';
      } else {
        this.error = '';
      }
    },
    limpiarError() {
      this.error = '';
    },



  },
  //TODO REPASAR ESTA FUNCION meterla cuando se 
  //da lick al boton plantar del componente Info.vue
  //COOKIES para saltarte toda la morralla:
  // beforeCreate(){
  //   const aboUsVisitedCookie= document.cookie.includes("about-us-visited=True");
  //   consolele.log(aboUsVisitedCookie);
  //   if(!aboUsVisitedCookie){
  //     this.$router.push("/main");
  //   }
  // },
  created() {
    this.obtenerTareas()
  }
};
</script>

<style>
.error-message {
  color: red;
  font-weight: bold;
}

.custom-icon {
  color: #f59e0b;
  /* Change the color */
  font-size: 24px;
  /* Change the size */
  margin-left: 10px;
  /* Add some margin */
}

.ok-button {
  background-color: #f44336;
  color: white;
  border: none;
  padding: 10px 20px;
  text-align: center;
  display: inline-block;
  font-size: 16px;
  margin-top: 10px;
  cursor: pointer;
}

.ok-button:hover {
  background-color: #d32f2f;
}

.error-container {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.tareas-lista {
  overflow-y: auto;
  padding: 10px;
  /* Optional: for some padding */
  box-sizing: border-box;
  /* Ensure padding is included in the height calculation */
}

.container {
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: center;
  height: 100vh;
}

.tareas {
  display: flex;
  flex-direction: column;
  height: 100vh;
  width: 50%;
}

.tareas h2,
button {
  margin-left: 50px;
}

.avatar-container {
  display: flex;
  align-items: flex-end;
  margin-top: auto;
  margin-bottom: 30px;
  margin-left: 50px;
}

.avatar {
  width: 50px;
}

.background-campo {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  background-color: #C0D470;
  background-image: url("@/components/imgs/farm/farm_land.png");
  background-size: cover;
  background-repeat: repeat;
  height: 100vh;
  width: 50%;
}

.numTareas p {
  display: flex;
  color: white;
}

.campo {
  display: grid;
  grid-template-columns: repeat(5, 1fr);
  grid-gap: 5px;
  width: 200px;
}

.parcela {
  width: 40px;
  height: 40px;
  background-size: cover;
  border-radius: 5px;
}



.card {
  border: 1px solid #ccc;
  margin: 10px;
  padding: 10px;
}

.card-header {
  font-weight: bold;
}

.card-body p {
  margin: 5px 0;
}

.avatar {
  position: relative;
  height: 5rem;
  width: 4rem;
  top: 150%
}
</style>
