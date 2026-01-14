<script>
import supabase from '@/library/supabase.js';

export default {
    data() {
        return {

        }
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
    <form @submit.prevent="login" class="flex flex-col gap-3 items-center justify-center" >
        <h1 class="w-23 text-center" >login as an admin here</h1>        
        <label for="email">
            <h1>email</h1>
            <input type="email" ref="email" name="email" id="email">
        </label>
        <label for="password">
            <h1>password</h1>
            <input type="password" ref="password" name="password" id="password">
        </label>
        <button type="submit">login</button>
    </form>
</template>