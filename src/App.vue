<script>
import MessageItems from './components/MessageItems.vue';
import {VueSpinner,VueSpinnerTail} from "vue3-spinners"
import Login from './components/Login.vue'
import supabase from './library/supabase';
export default {
    components: {
        MessageItems,
        Login,
        VueSpinner,
        VueSpinnerTail
    },
    data() {
        return {
            count: 1,
            data: null,
            admin: null,
            shownav: false,
            customerData:null,
        }
    },
    watch: {
    },
    methods: {
        async Getcustomer() {
            const { data, error } = await supabase.schema('demoservice').from('workTodo').select()
            // console.log(data);
            this.customerData = data
            return data
        },
    },    
    mounted() {
        supabase.auth.onAuthStateChange((_event, session) => {
            this.admin = !!session            
        })        
        this.Getcustomer()
    },    
    
    computed: {}
}
</script>

<template>
    <main v-if="customerData" class="w-screen flex flex-col gap-3 items-center justify-center  bg-zinc-100 ">
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
        <MessageItems :messagedata="customerData" :isadmin="admin" />
        <!-- <Login /> -->
    </main>
    <div v-else class="w-full h-screen flex flex-col justify-center items-center" >
        <VueSpinnerTail size="150" color="orange" />
         <h1 class="text-6xl text-orange-500 font-bold font-sora " > loading.... </h1>
    </div>
</template>
