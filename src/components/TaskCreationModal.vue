<template>
    <Dialog @update:visible="close" :visible="visible" modal header="Crear Tarea" :style="{ width: '25rem' }">
        <div class="flex flex-col gap-4">
            <span class="text-surface-500 dark:text-surface-400 block mb-8">Selecciona la categoría de tu tarea:</span>
            <Dropdown v-model="task.categoriaTarea" :options="categorias" optionLabel="name" class="w-full" />

            <span v-if="errors.categoriaTarea" class="text-red-500">{{ errors.categoriaTarea }}</span>
            <div class="flex flex-col gap-4">
                <label for="nombreTarea" class="font-semibold w-full">Nombre de la tarea:</label>
                <InputText v-model="task.nombreTarea" placeholder="Introduce el nombre de la tarea" class="w-full" />

                <span v-if="errors.nombreTarea" class="text-red-500">{{ errors.nombreTarea }}</span>
            </div>
            <div class="flex flex-col gap-4">
                <label for="fecha" class="font-semibold w-full">Fecha:</label>
                <Calendar v-model="task.fecha" dateFormat="dd/mm/yy" class="w-full" />
                <span v-if="errors.fecha" class="text-red-500">{{ errors.fecha }}</span>
            </div>
            <div class="flex justify-end gap-2">
                <Button type="button" label="Cancelar" severity="secondary" @click="close" class="p-button-outlined" />
                <Button type="button" label="Crear" @click="createTask" class="p-button-primary" />
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
export default {
    props: {
        visible: {
            type: Boolean,
            required: true
        },
        categorias: {
            type: Array,
            required: true
        }
    },
    data() {
        return {
            task: {
                nombreTarea: '',
                categoriaTarea: '',
                fecha: new Date()
            },
            errors: {}
        };
    },
    methods: {
        validateForm() {
            this.errors = {};
            if (!this.task.nombreTarea) {
                this.errors.nombreTarea = 'Nombre de la tarea es obligatorio';
            }
            if (!this.task.categoriaTarea) {
                this.errors.categoriaTarea = 'Categoría es obligatorio';
            }
            if (!this.task.fecha) {
                this.errors.fecha = 'Fecha es obligatorio';
            }
            return Object.keys(this.errors).length === 0;
        },
        close() {
            this.$emit('update:visible', false);
        },
        createTask() {
            if (this.validateForm()) {
                this.$emit('create-task', this.task);
                this.task = { nombreTarea: '', categoriaTarea: '', fecha: '' }; // Reset task after creation
                this.close();
           
            }
        }
    }
}
</script>
