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
        <div v-if="showmessage" class="fixed z-1000 top-17 left-15  w-72 h-26 border-4 shadow-[4px_4px_0_black]" :class="statusmessage ? 'bg-green-400 ' : 'bg-red-400'" 
            id="flashmessage">
            <button @click.stop="closemessage" class="absolute right-0 top-0">
                <i class="bi bi-plus text-4xl" ></i>
            </button>
            <div class="flex w-full h-full gap-2 items-center justify-between">
                <i class="bi text-6xl" :class="statusmessage ? 'bi-check': 'bi-x'" ></i>
                <h4 class="font-sora text-3xl"> {{ message }} </h4>
            </div>
        </div>
    </Transition>
</template>