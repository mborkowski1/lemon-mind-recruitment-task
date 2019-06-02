<template>
    <v-app>
        <v-content>
            <Main/>
        </v-content>
    </v-app>
</template>

<script>
    import Main from './views/Main'

    export default {
        name: 'App',
        components: {
            Main
        },
        data () {
            return {

            }
        }
    }
</script>
