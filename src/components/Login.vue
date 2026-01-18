<script>
import supabase from '@/library/supabase.js';
import Backdrop from './Backdrop.vue';

export default {
    props: ["closelogin", "showlogin"],
    data() {
        return {}
    },
    components: {
        Backdrop
    },
    methods: {
        async login() {
            const useremail = this.$refs.email.value
            const userpassword = this.$refs.password.value
            const { error } = await supabase.auth.signInWithPassword({
                email: useremail,
                password: userpassword,
            })
            if (error) {
                console.log(error)
                return
            }
        }
    },
}
</script>

<template>
    <Backdrop :closepreview="closelogin" :imgpreview="showlogin" >
        <form @click.stop @submit.prevent="login"
            class=" z-100 flex flex-col p-3 absolute top-45 left-15 lg:left-[40vw] bg-red-400 border-4 shadow-[4px_4px_0_black] gap-3 items-center justify-center">
            <h1 class="w-42 text-center font-sora text-2xl ">login as an admin here</h1>
            <label for="email">
                <h1 class="text-xl font-jakarta font-medium">email</h1>
                <input class="inputstyle" type="email" ref="email" name="email" id="email">
            </label>
            <label for="password">
                <h1 class="text-xl font-jakarta font-medium">password</h1>
                <input class="inputstyle" type="password" ref="password" name="password" id="password">
            </label>
            <button
                class="px-5 py-1 font-sora text-2xl font-semibold transition hover:scale-85 border-4 shadow-[4px_4px_0_black] bg-green-400"
                type="submit">login</button>
        </form>
    </Backdrop>
</template>