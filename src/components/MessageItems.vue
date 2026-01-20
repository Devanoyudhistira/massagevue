<script>
import supabase from '@/library/supabase.js';
import dayjs from '@/library/day.js';
import Backdrop from './Backdrop.vue';
import Flashmessage from './Flashmessage.vue';

export default {
    components: { Backdrop, Flashmessage },

    data() {
        return {
            customerData: this.messagedata,
            loading: true,
            imgpreview: false,
            previewAsset: "",
            statusbutton: false,
            showmessage: false,
            statusmessage:false,
            message:""
        }
    },
    props: ["isadmin", "messagedata", "deleteproduct", "formopen", "closeform","updateproject"],
    methods: {
        sendingWa(number,status) {
            let message = "";
            switch (status) {
                case "finish":
                    message = "barang anda sudah selesai dan sudah bisa di ambil"
                    break;                
                case "pending":
                    message = "barang sedang dalam perbaikan"
                    break;                
                default:
                    message = "barang anda memiliki masalah segera ke warung"
                    break;
            }
            const encodedMessage = encodeURIComponent(message);
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
        openstatus(cardtarget) {
            const statustarget = this.$refs.productcard[cardtarget]
            const projecttarget = this.$refs.projectcard[cardtarget]
            this.statusbutton = !this.statusbutton
            if (this.statusbutton)
                statustarget.classList.replace('hidden', 'flex')
            else {
                this.$refs.productcard.forEach(e => {
                    e.classList.replace('flex', 'hidden')
                })
            }
        },
        
        async addproject() {
            const { data, error } = await supabase
                .schema('demoservice').from('workTodo')
                .insert({
                    AtasNama: this.$refs.atasnama.value,
                    Deskripsikerusakan: "tidak keluar suara",
                    Image: "namafilerandom",
                    Status: "pending",
                    Biaya: this.$refs.biaya.value,
                    Nomer_hp: this.$refs.nomorhp.value,
                    Namabarang: this.$refs.namaproduk.value
                }).select()
                .single()
            if (data) {
                this.closeform()                
                this.messagedata.push(data)
                this.showmessage = true
                this.statusmessage = true,
                this.message = "sucesfully created"
                return
            }
            this.showmessage = true
            this.statusmessage = false,
            this.message = "creation fail"
            return            
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
        this.customerdata = this.messagedata
        console.log(this.messagedata[0].Biaya)
    },
    computed: {
        allproject(){
            return this.messagedata
        }
    },
    watch: {

    },

}
</script>

<template>
    <Flashmessage :showmessage="showmessage" :message="message" :statusmessage="statusmessage"
        :closemessage="() => showmessage = false" />
    <article
        class=" w-full h-80vw overflow-y-hidden px-2 pb-28 grid grid-cols-1 gap-4 place-items-center overflow-x-hidden mt-18">
        <div v-for="(items, index) in allproject" ref="projectcard"
            class="w-80 relative px-3 py-2 gap-4 rounded-xl overflow-hidden border-4 tranform origin-top border-black shadow-[4px_4px_0_black] font-sans flex flex-col justify-between transition duration-300"
            :class="[
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
                <p class="font-jakarta font-semibold text-[13px] text-zinc-900"> {{ items.Namabarang }}  </p>
                <p class="font-jakarta font-semibold text-[13px] text-zinc-900">Rp. {{ items.Biaya.toFixed(3) }}  </p>
                <p class="font-sora font-medium text-[14px] text-zinc-900"> {{ items.Deskripsikerusakan }} </p>
                <div v-show="isadmin" @click="() => openstatus(index)"
                    class="w-full h-max flex justify-end cursor-pointer">
                    <i class="duration-200 bi bi-chevron-down text-3xl"
                        :class="statusbutton ? 'rotate-180' : 'rotate-0'"></i>
                </div>
            </div>
            <Transition>
                <div ref="productcard" v-show="isadmin && statusbutton" class="hidden w-full justify-between flex-col gap-2">
                    <div class="flex gap-2">
                        <button @click="() => updateproject('error',items.id)" class="statusbutton bg-red-700">alert</button>
                        <button @click="() => updateproject('finish',items.id)" class="statusbutton bg-sky-400 ">Done</button>
                        <button @click="() => updateproject('pending',items.id)" class="statusbutton bg-yellow-400 ">Proses</button>
                    </div>

                    <div class="flex gap-2">
                        <button @click="() => deleteproduct(items.id)"
                            class="w-12 h-12  bg-red-600 text-black text-md border-2 border-black shadow-[3px_3px_0_black]">
                            <i class="bi bi-trash text-3xl ">
                            </i> </button>

                        <button @click="() => sendingWa(items.Nomer_hp,items.Status)"
                            class="w-12 h-12  bg-green-600 text-black text-md border-2 border-black shadow-[3px_3px_0_black]">
                            <i class="bi bi-whatsapp text-2xl ">
                            </i> </button>
                    </div>
                </div>
            </Transition>
        </div>
    </article>

    <Backdrop :closepreview="closepreview" :imgpreview="imgpreview">
        <img @click.stop class="object-center object-cover w-85 h-60" src="./../assets/image1.jpg" alt="" srcset="">
    </Backdrop>

    <Backdrop :closepreview="closeform" :imgpreview="formopen">
        <form
            class="-mt-8 w-max h-max px-2 py-3 flex flex-col gap-3 items-center justify-center bg-blue-500 shadow-[4px_4px_0_black] border-3 "
            action="" @submit.prevent="addproject" @click.stop>
            <h1 class="text-3xl font-sora font-bold"> new project </h1>
            <label for="atasnama">
                <h3 class="inputtext"> atas nama</h3>
                <input autocomplete="off" ref="atasnama" class="inputstyle" type="text" name="atasnama" id="atasnama">
            </label>
            <label for="namaproduk">
                <h3 class="inputtext"> nama produk </h3>
                <input ref="namaproduk" class="inputstyle" type="text" name="namaproduk" id="namaproduk">
            </label>
            <label for="nomorhp">
                <h3 class="inputtext"> nomor hp </h3>
                <input autocomplete="off" ref="nomorhp" class="inputstyle" type="number" name="nomorhp" id="nomorhp">
            </label>
            <label for="Biaya">
                <h3 class="inputtext"> Biaya </h3>
                <input autocomplete="off" ref="biaya" class="inputstyle" type="number" name="nomorhp" id="nomorhp">
            </label>
            <button type="submit"
                class="w-[80%] shadow-[4px_4px_0_black] border-4 hover:scale-90 transition h-max bg-green-500 py-2 font-sora text-2xl font-extrabold">
                confirm
            </button>
        </form>
    </Backdrop>

</template>