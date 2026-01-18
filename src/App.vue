<script>
import MessageItems from './components/MessageItems.vue';
import { VueSpinner, VueSpinnerTail } from "vue3-spinners"
import Login from './components/Login.vue'
import Flashmessage from './components/Flashmessage.vue';
import supabase from './library/supabase';
import Footer from './components/Footer.vue';
export default {
    components: {
        MessageItems,
        Login,
        VueSpinner,
        VueSpinnerTail,
        Flashmessage,
        Footer
    },
    data() {
        return {
            count: 1,
            data: null,
            admin: null,
            shownav: false,
            customerData: null,
            loginform: false,
            formopen:false,
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
        async updateproject(status, target) {
            const { error, data } = await supabase.schema("demoservice")
                .from('workTodo').update({ Status: status }).eq('id', target)
                .select().single()
            if (data) {                
                    this.customerData = this.customerData.map(e =>
                        e.id === target
                            ? { ...e, Status: status }
                            : e
                    )                
            }
            return
        },
        closeloginform() {
            this.loginform = false
        },
        openform() {
            this.shownav = false
            this.loginform = true
        },
        async deleteproduct(target) {
            console.log(target)
            const { count, data, error } = await supabase.schema("demoservice").from("workTodo").delete().eq('id', target);
            const deleteresult = this.customerData.filter(e => e.id !== target)
            this.customerData = deleteresult
            console.log(deleteresult)
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
    <Login :closelogin="closeloginform" :showlogin="loginform" />
    <main v-if="customerData" class="w-screen overflow-x-hidden flex flex-col gap-3 items-center justify-center ">
        <Flashmessage />
        <nav  class="bg-zinc-900 flex items-center justify-between px-2 w-full h-16">
            <h1 class="text-3xl font-sora text-zinc-100"> Tailors </h1>
            <div v-show="!admin" class="flex flex-col gap-2 z-20000" @click="shownav = !shownav">
                <span class="navitem"></span>
                <span class="navitem"></span>
                <span class="navitem"></span>
            </div>
        </nav>
        <Transition name="navanimate">
            <div v-if="shownav"
                class="w-[70%] h-screen duration-200 bg-orange-400 fixed z-10000 top-0 right-0 flex justify-end flex-col">
                <div class="w-full mb-8 h-max flex justify-center items-center">
                    <button @click="openform"
                        class="w-[80%] mb-3 h-15 border-black border-4 text-white cursor-pointer hover:scale-90 transition shadow-[4px_4px_0_black]"
                        :class="admin ? 'bg-blue-400' : 'bg-red-400'">
                        <h3 class="font-sora font-semibold text-black text-3xl"> {{ admin ? 'create' : 'log out' }}</h3>
                    </button>
                </div>
            </div>
        </Transition>
        <MessageItems :updateproject="updateproject" :closeform="() => this.formopen = false" :formopen="formopen" :deleteproduct="deleteproduct" :messagedata="customerData" :isadmin="admin" />
        <Footer>
            <!--  -->
            <button v-show="admin" @click="formopen = true"
                class=" cursor-pointer  w-12 h-12 bg-orange-600 shadow-[4px_4px_0_black] border-2">
                <i class="bi bi-plus text-3xl"></i>
            </button>
        </Footer>
    </main>
    <div v-else class="w-full h-screen flex flex-col justify-center items-center">
        <VueSpinnerTail size="150" color="orange" />
        <h1 class="text-6xl text-orange-500 font-bold font-sora "> loading.... </h1>
    </div>
</template>
