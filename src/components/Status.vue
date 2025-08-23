<template>
    <div id="status">
        <div v-if="id != null">
            <p>Waiting for opponent...</p>
            <div id="id">
                <p>Your game id: <span id="idnumber">{{ id }}</span></p>
            </div>
            
        </div>

        <div v-else>
            <p>{{ msg }}</p>
        </div>
    </div>

</template>

<script setup>

const props = defineProps({
    id: String,
    msg: String
})

</script>

<style scoped>

#id {
    padding: 0;
    border-radius: 5px;
    border: solid 1px;
}

#idnumber{
    color: dodgerblue;
    font-weight: bold;
}

#status {
    font-size: xx-large;
}

</style>