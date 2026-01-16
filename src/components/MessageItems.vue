<script>
import supabase from '@/library/supabase.js';
import dayjs from '@/library/day.js';
import Backdrop from './backdrop.vue';

export default {
    components: { Backdrop },

    data() {
        return {
            customerData: [],
            loading: true,
            imgpreview: false,
            previewAsset: "",
            statusbutton: false,
            formopen:false
        }
    },
    props: ["isadmin", "messagedata"],
    methods: {
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
            this.imgpreview = true
        },
        closepreview() {
            this.imgpreview = false
        },
        closeform() {
            this.formopen = false
        },
        async addproject() {
            const { data, error } = await supabase
                .schema('demoservice').from('workTodo')
                .insert({
                    AtasNama: this.$refs.atasnama.value,
                    Deskripsikerusakan: "tidak keluar suara",
                    Image: "namafilerandom",
                    Status: "pending",
                    Biaya: 0.00,
                    Nomer_hp:this.$refs.nomorhp.value,
                    Namabarang: this.$refs.namaproduk.value
                }).select()
                .single()
                console.log(data)
                console.log(error)
        },
        statusClass(itemStatus) {
            if (itemStatus === 'finish') {
                return 'bg-blue-400'
            } else if (itemStatus === 'pending') {
                return 'bg-yellow-400'
            } else {
                return 'bg-red-600'
            }
        }
    },
    mounted() {
        console.log(this.messagedata)
    },
    computed: {},
    watch: {},

}
</script>

<template>
    <article v-for="items in messagedata" class=" w-full pb-6 grid grid-cols-1 gap-4 place-items-center">
        <div class="w-90 relative px-3 py-2 gap-4 rounded-xl overflow-hidden  border-4 tranform origin-top border-black shadow-[4px_4px_0_black] font-sans flex flex-col justify-between transition duration-300"
            :class="[
                statusbutton ? 'h-82' : 'h-max',
                statusClass(items.Status)
            ]">
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
                <p class="font-jakarta font-semibold text-[13px] text-zinc-900"> laptop asus 900 </p>
                <p class="font-sora font-medium text-[14px] text-zinc-900"> {{ items.Deskripsikerusakan }} </p>
                <div v-show="isadmin" @click="statusbutton = !statusbutton"
                    class="w-full h-max flex justify-end cursor-pointer">
                    <i class="duration-200 bi bi-chevron-down text-3xl"
                        :class="statusbutton ? 'rotate-180' : 'rotate-0'"></i>
                </div>
            </div>
            <Transition>
                <div v-show="statusbutton && isadmin" class="flex w-full justify-between">
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
           @click="formopen = true" class="absolute cursor-pointer  w-12 h-12 bottom-5 right-5 bg-orange-600 shadow-[4px_4px_0_black] border-2">
            <i class="bi bi-plus text-3xl"></i>
        </button>
    </article>
    <Backdrop :closepreview="closepreview" :imgpreview="imgpreview">
        <img @click.stop class="object-center object-cover w-85 h-60" src="./../assets/image1.jpg" alt="" srcset="">
    </Backdrop>
    <Backdrop :closepreview="closeform" :imgpreview="formopen">
        <form class="w-max h-max px-2 py-3 flex flex-col gap-3 items-center justify-center bg-blue-500 shadow-[4px_4px_0_black] border-3 " action=""
            @submit.prevent="addproject" @click.stop>
            <h1 class="text-3xl font-sora font-bold" >  new project </h1>
            <label for="atasnama">
                <h3 class="inputtext"> atas nama</h3>
                <input ref="atasnama" class="inputstyle" type="text" name="atasnama" id="atasnama">
            </label>
            <label for="namaproduk">
                <h3 class="inputtext"> nama produk </h3>
                <input ref="namaproduk" class="inputstyle" type="text" name="namaproduk" id="namaproduk">
            </label>
            <label for="nomorhp">
                <h3 class="inputtext"> nomor hp </h3>
                <input ref="nomorhp" class="inputstyle" type="number" name="nomorhp" id="nomorhp">
            </label>
            <button type="submit" class="w-[80%] shadow-[4px_4px_0_black] border-4 hover:scale-90 transition h-max bg-green-500 py-2 font-sora text-2xl font-extrabold"> confirm </button>
        </form>
    </Backdrop>

</template>