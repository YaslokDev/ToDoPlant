<template>
    <Dialog @update:visible="close" :visible="visible" modal header="Editar Tarea" :style="{ width: '25rem' }">
        <div class="flex flex-col gap-4">
            <span class="text-surface-500 dark:text-surface-400 block mb-8">Selecciona la categoría de tu tarea:</span>
            <Dropdown v-model="selectedCategory" :options="categorias" optionLabel="name" optionValue="name"
                placeholder="Select a Category" />

            <span v-if="errors.categoriaTarea" class="text-red-500">{{ errors.categoriaTarea }}</span>
            <div class="flex flex-col gap-4">
                <label for="nombreTarea" class="font-semibold w-full">Nombre de la tarea:</label>
                <InputText v-model="localTask.text" placeholder="Introduce el nombre de la tarea" class="w-full" />

                <span v-if="errors.nombreTarea" class="text-red-500">{{ errors.nombreTarea }}</span>
            </div>
            <div class="flex flex-col gap-4">
                <label for="fecha" class="font-semibold w-full">Fecha:</label>
                <Calendar v-model="localTask.createdAt" dateFormat="dd/mm/yy" class="w-full" />
                <span v-if="errors.fecha" class="text-red-500">{{ errors.fecha }}</span>
            </div>
            <div class="flex justify-end gap-2">
                <Button type="button" label="Cancelar" severity="secondary" @click="close" class="p-button-outlined" />
                <Button type="button" label="Guardar" @click="editTask" class="p-button-primary" />
            </div>
        </div>
    </Dialog>
</template>

<style scoped>
.flex {
    display: flex;
}

.flex-col {
    flex-direction: column;
}

.gap-4 {
    gap: 1rem;
}

.w-full {
    width: 100%;
}

.text-red-500 {
    color: red;
}
</style>

<script>
import { format } from 'date-fns';

export default {
    props: {
        visible: {
            type: Boolean,
            required: true
        },
        categorias: {
            type: Array,
            required: true
        },
        task: {
            type: Object,
            default: () => ({})
        }
    },
    data() {
        return {
            localTask: {},
            errors: {},
            selectedCategory: null
        };
    },
    watch: {
        task: {
            handler(newTask) {
                console.log('Datos de la tarea recibidos en el modal:', newTask);
                this.localTask = { ...newTask };
                this.selectedCategory = newTask ? newTask.description : null;
                if (newTask && newTask.createdAt) {
                    this.localTask.createdAt = format(new Date(newTask.createdAt), 'dd/MM/yyyy');
                }
                console.log('Datos de localTask:', this.localTask);
            },
            immediate: true
        }
    },
    mounted() {
        console.log('Componente montado con localTask:', this.localTask);
    },
    methods: {
        validateForm() {
            this.errors = {};
            if (!this.localTask.text) {
                this.errors.nombreTarea = 'Nombre de la tarea es obligatorio';
            }
            if (!this.selectedCategory) {
                this.errors.categoriaTarea = 'Categoría es obligatorio';
            }

            return Object.keys(this.errors).length === 0;
        },

        editTask() {
            console.log("ESTOY DENTRO DEL EDITTASK", this.localTask)
            if (this.validateForm()) {
                console.log("TEXT ID: ", this.localTask.id)
                console.log("TEXT: ", this.localTask.text)
                console.log("TEXT DESCRIPTION: ", this.selectedCategory)



                // Realiza el fetch PATCH aquí
                let id = this.localTask.id
                fetch(`https://node-todos.vercel.app/users/pollo/todos/${id}`, {
                    method: 'PATCH',
                    headers: {
                        'Content-Type': 'application/json',
                    },
                    body: JSON.stringify({
                        id: this.localTask.id,
                        text: this.localTask.text,
                        description: this.selectedCategory,
                        completed: false,
                        author: 'pollo',
                        tags: ["beach"],
                        createdAt: "2020-03-10T04:05:06.157Z"
                    }),

                })

                    .then((response) => response.json())
                    .then((data) => {
                        console.log('Tarea actualizada:', data);
                        this.$emit('edit-task', data);
                        this.close();
                    })
                    .catch((error) => {
                        console.error('Error al actualizar la tarea:', error);
                    });
            }
        },
        close() {
            this.$emit('update:visible', false);
        },
    }
}
</script>