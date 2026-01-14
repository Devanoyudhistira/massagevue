<script>
import MessageItems from './components/MessageItems.vue';
import Login from './components/Login.vue'
import supabase from './library/supabase';
export default {
    components: {
        MessageItems,
        Login
    },
    data() {
        return {
            count: 1,
            data: null,
            admin: null,
            shownav: false,
        }
    },
    watch: {
    },
    mounted() {
        supabase.auth.onAuthStateChange((_event, session) => {
            this.admin = !!session
            console.log('AUTH EVENT:', _event)
            console.log('SESSION:', session)
        })
    },
    methods: {
        
    },
    computed: {}
}
</script>

<template>
    <main v-if="admin" class="w-screen flex flex-col gap-3 items-center justify-center  bg-zinc-100 ">
        <nav class="bg-zinc-900 flex items-center justify-between px-2 w-full h-16">
            <h1 class="text-3xl font-sora text-zinc-100"> Tailors </h1>
            <div class="flex flex-col gap-2 z-100" @click="shownav = !shownav">
                <span class="navitem"></span>
                <span class="navitem"></span>
                <span class="navitem"></span>
            </div>
        </nav>
        <Transition name="navanimate">
            <div v-if="shownav" class="w-screen h-screen bg-zinc-800/50 fixed top-0 left-0"></div>
        </Transition>
        <MessageItems isadmin="this.admin" />
        <!-- <Login /> -->
    </main>
    <div v-else >
         <h1 class="text-4xl font-sora" > loading.... </h1>
    </div>
</template>
