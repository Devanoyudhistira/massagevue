<script>
import supabase from '@/library/supabase.js';
export default {

    data() {
        return {
            customerData: [],
            loading: true
        }
    },
    props: ["isadmin"],
    methods: {
        async Getcustomer() {
            const { data, error } = await supabase.schema('demoservice').from('workTodo').select()
            this.customerData = data
            console.log(error)
            return data
        },
        sendingWa(number) {
            let massage = "barang sudah selesai diperbaiki segera datang ke kantor"
            const encodedMessage = encodeURIComponent(massage);
            const whatsappUrl = `https://wa.me/+${number}?text=${encodedMessage}`;

            window.open(whatsappUrl, '_blank');
            return massage
        },
    },
    watch: {
        customerData() {
            if (this.customerData) {
                this.loading = false
                console.log(this.customerData)
            }
        }
    },
    mounted() {
        this.Getcustomer()
    }
}
</script>

<template>
    <article v-for="items in customerData" class=" w-full grid grid-cols-1 place-items-center">
        <div
            class="w-90 px-3 py-1 gap-4 rounded-xl h-max bg-zinc-50 border-3 border-red-600 font-sans flex flex-col justify-between">
            <div>
                <h1 class="text-2xl font-jakarta font-semibold "> {{ items.AtasNama }} </h1>
                <p> </p>
            </div>
            <div class="flex w-full justify-between ">
                <div class="flex gap-2">
                    <button class="border-3 border-red-700  px-4 py-1 rounded-md ">alert</button>
                    <button class="border-3 border-sky-400  px-4 py-1 rounded-md ">Done</button>
                </div>
                <button @click="() => sendingWa(items.Nomer_hp)"
                    class="w-12 h-12 rounded-full text-black text-md bg-green-600"> <i class="bi bi-whatsapp text-2xl">
                    </i> </button>
            </div>
        </div>
    </article>
</template>