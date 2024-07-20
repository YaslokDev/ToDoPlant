<template>
    <Dialog @update:visible="close" :visible="visible" modal header="¡Que aproveche!" :style="{ width: '20rem' }">

        <p></p>
        <div class="flex justify-end gap-2 mt-4">
            <img src="/src/components/imgs/animated_gifs/growth_plant.gif" alt="growth">
            <Button label="Recoger" @click="close" class="p-button-primary">Gracias</Button>

        </div>
    </Dialog>
</template>

<script>
export default {
    props: {
        visible: {
            type: Boolean,
            required: true
        },
        taskId: {
            type: String,
            required: true
        }
    },
    methods: {
        close() {
            location.reload(); 
            this.$emit('update:visible', false);
        },
    }
}
</script>