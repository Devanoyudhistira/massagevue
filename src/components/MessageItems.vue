<script>
import supabase from '@/library/supabase.js';
import dayjs from '@/library/day.js';

export default {
    data() {
        return {
            customerData: [],
            loading: true,
            imgpreview: false,
            previewAsset: "",
            statusbutton: false
        }
    },
    props: ["isadmin"],
    methods: {
        async Getcustomer() {
            const { data, error } = await supabase.schema('demoservice').from('workTodo').select()
            this.customerData = data
            return data
        },
        sendingWa(number) {
            let massage = "barang sudah selesai diperbaiki segera datang ke kantor"
            const encodedMessage = encodeURIComponent(massage);
            const whatsappUrl = `https://wa.me/+${number}?text=${encodedMessage}`;

            window.open(whatsappUrl, '_blank');
            return massage
        },
        created(date) {
            return dayjs(date).fromNow()
        },
        openpreview(image) {
            console.log(image)
            this.imgpreview = true
        },
        closepreview() {
            this.imgpreview = false
        },
       async addproject() {
            const { data, error } = await supabase
                .schema('demoservice').from('products')
                .insert({
                    AtasNama: "productname",
                    Deskripsikerusakan: "",
                    image: "namafilerandom",
                    status: false,
                    biaya:123,
                    nomer:12223,
                    Namabarang:""
                }).select()
                .single()
        }
    },
    watch: {
        customerData() {
            if (this.customerData) {
                this.loading = false
            }
        }
    },
    mounted() {
        this.Getcustomer()
    }
}
</script>

<template>
    <article v-for="items in customerData" class=" w-full grid grid-cols-1 gap-4 place-items-center">
        <div class="w-90 relative px-3 py-2 gap-4 rounded-xl overflow-hidden bg-blue-400 border-3 tranform origin-top border-blue-600 shadow-[4px_4px_0_blue] font-sans flex flex-col justify-between transition duration-300"
            :class="statusbutton ? 'h-82' : 'h-max'">
            <img class="object-center object-cover w-full h-30" src="./../assets/image1.jpg" alt="" srcset="">
            <div class="flex flex-col">

                <button @click="openpreview"
                    class="absolute hover:scale-80 transition focus:scale-70 w-8 h-8 rounded-full bg-zinc-100 shadow-[3px_3px_0_black] top-22 right-4 ">
                    <i class="bi bi-arrows-angle-expand text-xl "></i>
                </button>

                <div class="w-full flex justify-between items-center">
                    <h1 class="text-3xl font-jakarta font-bold "> {{ items.AtasNama }} </h1>
                    <h4 class="max text-[14px] text-center font-jakarta font-semibold"> {{ created(items.created_at) }}
                    </h4>
                </div>
                <p> {{ items.Deskripsikerusakan }} </p>
                <div v-show="isadmin" @click="statusbutton = !statusbutton"
                    class="w-full h-max flex justify-end cursor-pointer">
                    <i class="duration-200 bi bi-chevron-down text-3xl"
                        :class="statusbutton ? 'rotate-180' : 'rotate-0'"></i>
                </div>
            </div>
            <Transition name="navanimate">
                <div v-show="statusbutton" class="flex w-full justify-between">
                    <div class="flex gap-2">
                        <button class="statusbutton bg-red-700">alert</button>
                        <button class="statusbutton bg-sky-400 ">Done</button>
                    </div>
                    <button @click="() => sendingWa(items.Nomer_hp)"
                        class="w-12 h-12  bg-green-600 text-black text-md border-2 border-black shadow-[3px_3px_0_black]">
                        <i class="bi bi-whatsapp text-2xl ">
                        </i> </button>
                </div>
            </Transition>
        </div>
        <button
            class="absolute cursor-pointer  w-12 h-12 bottom-5 right-5 bg-orange-600 shadow-[4px_4px_0_black] border-2">
            <i class="bi bi-plus text-3xl"></i>
        </button>
    </article>
    <Transition name="navanimate">
        <div @click.stop="closepreview" v-show="imgpreview"
            class="fixed top-0 left-0 bg-zinc-900/20 w-screen h-screen flex justify-center items-center">
            <img class="object-center object-cover w-85 h-60" src="./../assets/image1.jpg" alt="" srcset="">
        </div>
    </Transition>

</template>