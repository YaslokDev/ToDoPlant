<template>
  <div class="container">
    <div class="tareas">
      <div>
        <h2 style="text-align: left;">To Do list</h2>
      </div>
      <div>
        <Button label="Primary" @click="visible = true">Crear tarea</Button>
      </div>

      <!-- Lista de Tareas en tarjetas -->
      <div class="tareas-lista">
        <h3 style="margin-left:10px">Tareas Actuales:</h3>
        <ProgressSpinner v-if="this.loading" style="width: 50px; height: 50px" strokeWidth="3" fill="transparent"
          animationDuration=".5s" aria-label="Custom ProgressSpinner" />


        <TaskCard v-for="(tarea, index) in tareas" :key="tarea.nombreTarea" :tarea="tarea" @delete="borrarTarea(index)" 
          @edit="openEditModal(tarea, index)" @complete="completarTarea(index)" />

      </div>

      <div class="avatarMove">
        <img :src="`/imgs/avatars/${selectedAvatarStore.avatar}.png`" class="avatar">
      </div>

      <!-- Audio -->
      <!-- <div id="sound">
        <Button type="button" value="sound" @click="playMusic" />
      </div> -->
      <audio loop autoplay controls volume="0.2" class="play-icon">
        <source src="@/components/audio/flute.wav" type="audio/wav">
      </audio>

      <audio loop autoplay>
        <source src="@/components/audio/bird-whistling-ambience.mp3" type="audio/mp3">
      </audio>

      <audio>
        <source src="@/components/audio/cow-mooing-loudly.mp3" type="audio/mp3">
      </audio>

      <!-- Modal para crear tarea -->
      <TaskCreationModal :visible="visible" :categorias="categorias" @update:visible="visible = $event"
        @create-task="agregarTarea" />

      <!-- Update the ConfirmationModal component usage -->
      <ConfirmationModal :visible="confirmVisible" @update:visible="handleVisibilityChange" @confirm="confirmDelete"
        :taskId="getTaskId()" />

      <!-- HECHO -->
      <CompleteModal :visible="completeVisible" @update:visible="handleVisibilityChangeComplete"
        @confirm="confirmComplete" :taskId="getTaskId()" />

      <!-- Modal para editar tarea -->
      <EditTaskModal v-if="taskToEdit" :visible="editVisible" :categorias="categorias" :task="taskToEdit"
        @update:visible="editVisible = $event" @edit-task="updateTask" />

      <ShowGifCompleteModal :visible="showGifCompleteVisible" @update:visible="handleShowVisibilityChange"
        @confirm="showConfirmModal" />

    </div>

    <div class="background-campo">
      <div class="borde-campo"></div>
      <div class="campo">
        <div @click="() => console.log(index)" v-for="(parcela, index) in Array(20)" s :key="index" class="parcela"
          :style="estiloParcela(index)"
          ></div>
      

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
      <!-- COWS -->
      <div class="cow-container cowMove">
        <img src="@/components/imgs/animated_gifs/animated_cow.gif" alt="moving cow" class="cow" id="cow-gif"
          @click="playCowSound">
      </div>


    </div>

  </div>

</template>

<script>
import ConfirmationModal from './ConfirmationModal.vue';
import TaskCard from './TaskCard.vue';
import TaskCreationModal from './TaskCreationModal.vue';
import EditTaskModal from './EditTaskModal.vue';
import CompleteModal from './CompleteModal.vue';
import { useSelectedAvatarStore } from "@/stores/selectedAvatar";
import ShowGifCompleteModal from './ShowGifCompleteModal.vue';

import cowSoundFile from '@/components/audio/cow-mooing-loudly.mp3';


const username = "aleh";
const API_URL = `https://node-todos.vercel.app/users/${username}`;

//get method:https://node-todos.vercel.app/users/aleh/todos
//documentacion: https://node-todos.vercel.app/api-docs/

// https://date-fns.org/ para descargar fechas

export default {
  setup() {
    const selectedAvatarStore = useSelectedAvatarStore()
    return { selectedAvatarStore }
  },
  components: {
    ConfirmationModal,
    TaskCard,
    TaskCreationModal,
    EditTaskModal,
    CompleteModal,
    ShowGifCompleteModal
  },
  data() {
    return {

      //Petiones GET para obtener info de la API
      //https://node-todos.vercel.app/api-docs/#/todos
      loading: false,

      tareas: [],

      visible: false,
      visible2: false,
      error: '',
      categorias: [
        { name: 'Personal', code: 'cat01' },
        { name: 'Trabajo', code: 'cat02' },
        { name: 'Hogar', code: 'cat03' },
        { name: 'Estudio', code: 'cat04' },
        { name: 'Otro', code: 'cat05' }
      ],
      confirmVisible: false,
      completeVisible: false,
      deleteIndex: null,
      editVisible: false,
      taskToEdit: null,
      editIndex: null,
      showGifCompleteVisible: false,
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
    playCowSound() {
      const cowSound = new Audio(cowSoundFile);

      cowSound.addEventListener('error', function (event) {
        console.error('Error al cargar el archivo de sonido:', event);
      });

      cowSound.play().catch((error) => {
        console.error('Error al reproducir el sonido:', error);
      });
    },


    openEditModal(task, index) {
      console.log('Tarea seleccionada para editar:', task); // Añade este log

      this.taskToEdit = { ...task };
      this.editIndex = index;
      this.editVisible = true;
    },

    updateTask(updatedTask) {
      this.tareas.splice(this.editIndex, 1, updatedTask);
      this.editVisible = false;
      this.taskToEdit = null;
      this.editIndex = null;
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

    showCompleteModal() {
      this.completeVisible = true;
    },

    showConfirmModal() {
      this.showGifCompleteVisible = false;
    },

    completarTarea(index) {
      this.deleteIndex = index;
      this.completeVisible = true;
    },

    confirmComplete() {
      this.tareas.splice(this.deleteIndex, 1);
      this.completeVisible = false;
      this.showGifCompleteVisible = true;
      this.deleteIndex = null;
    }, 

    handleVisibilityChange(newValue) {
      this.confirmVisible = newValue;
    },

    handleVisibilityChangeComplete(newValue) {
      this.completeVisible = newValue;
    },

    handleShowVisibilityChange(newValue) {
      this.showGifCompleteVisible = newValue;
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

    getTaskId() {
      if (this.deleteIndex !== null && this.tareas[this.deleteIndex]) {
        return this.tareas[this.deleteIndex].id;
      } else {
        return null;
      }
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
  },

  // audio button
  playMusic() {
    var music = new Audio('@/components/audio/flute.wav');
    music.play();
  }
};
</script>

<style>

.header-align{
  text-align:left;
}
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
  flex-wrap: wrap;
  padding: 35px;
  box-sizing: border-box;

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
  background-color: #f2f2f2;
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
/* .p-card p-component tareas-card{
  width: 0;

} */

.avatar {
  position: relative;
  height: 5rem;
  width: 4rem;
  margin-left:40px
}


/* COW */

.cow {
  position: relative;
  height: 7rem;
  width: 7rem;
}

.cowMove {
  position: absolute;
  left: 70%;
  bottom: 100px;
  animation: cow-animation 30s ease-in-out infinite;
}



@keyframes cow-animation {
  0% {
    transform: translate(0, 0) scaleX(1);
  }

  10% {
    transform: translate(10vw, 10vh);
  }

  20% {
    transform: translate(10vw, 10vh) scaleX(-1);
  }

  30% {
    transform: translate(10vw, 0vh) scaleX(-1);
  }

  40% {
    transform: translate(10vw, 0vh) scaleX(-1);
  }

  50% {
    transform: translate(0vw, 10vh) scaleX(-1);
  }

  60% {
    transform: translate(0vw, 10vh) scaleX(1);
  }

  70% {
    transform: translate(10vw, 10vh) scaleX(1);
  }

  75% {
    transform: translate(10vw, 10vh) scaleX(-1);
  }

  80% {
    transform: translate(10vw, 10vh) scaleX(-1);
  }

  90% {
    transform: translate(0vw, 0vh) scaleX(-1);
  }


}
</style>