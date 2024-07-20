<!-- Pagina de Bienvenida/Pagina principal -->

<template>
    <div class="container">
        <img alt="Vue logo" src="../components/imgs/todoplant_logo.png" />
        <h1>¡Bienvenido a tu listado de tareas en formato granero!</h1>

        <router-link to="/avatar">
            <Button type="button" label="Empezar" class="p-button" />
        </router-link>

    </div>
</template>

<script>
</script>

<style scoped>
.container {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: 100vh;
}
</style>