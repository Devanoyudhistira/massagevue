<script>


export default {
    props: ["showmessage","message","statusmessage","closemessage"],
    data() {
        return {}
    },
    methods: {        

    },
}
</script>

<template>
    <Transition name="flashmessage">
        <div v-if="showmessage" class="fixed z-10000 top-17 left-10  w-72 h-16 border-4 shadow-[4px_4px_0_black]" :class="statusmessage ? 'bg-green-400 ' : 'bg-red-400'" 
            id="flashmessage">
            <button @click.stop="closemessage" class="absolute right-0 top-2">
                <i class="bi bi-x text-red-600 text-3xl" ></i>
            </button>
            <div class="flex w-full h-full gap-2 items-center">
                <i class="bi text-4xl" :class="statusmessage ? 'bi-check': 'bi-x'" ></i>
                <h4 class="font-sora text-xl"> {{ message }} </h4>
            </div>
        </div>
    </Transition>
</template>