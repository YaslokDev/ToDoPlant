<template>
  <Card :style="{ width: '90%', overflow: 'hidden', margin: '10px'}">
    <template #title>{{ tarea.text }}</template>
    <template #subtitle><span v-text="tarea.description"></span></template>
    <template #content>
      <p class="m-0">
        Día de la tarea: {{ formatDate(tarea.createdAt) }}
      </p>
    </template>
    <template #footer>
      <div class="flex gap-4 mt-1 justify-between">
        <Button label="Conseguida" class="w-full" @click="emitComplete"><font-awesome-icon
            :icon="['fas', 'check']" /></Button>
        <Button label="Editar" @click="emitEdit" class="w-full"><font-awesome-icon :icon="['fas', 'pen']" /></Button>
        <Button label="Borrar" @click="emitDelete" class="w-full"><font-awesome-icon
            :icon="['fas', 'trash']" /></Button>
      </div>
    </template>
  </Card>
</template>

<script>
export default {
  props: {
    tarea: {
      type: Object,
      required: true
    }
  },
  methods: {
    formatDate(date) {
      return new Date(date).toLocaleDateString('es-ES', { day: '2-digit', month: '2-digit', year: 'numeric' });
    },
    emitDelete() {
      this.$emit('delete', this.tarea);
    },
    emitEdit() {
      this.$emit('edit', this.tarea);
    },
    emitComplete() {
      this.$emit('complete', this.tarea);
    },
  }
}
</script>