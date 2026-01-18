<script>
export default {
    props: ["closepreview","imgpreview"]
}
</script>

<template>
    <Transition name="formanimate" >
        <div @click.stop="closepreview" v-show="imgpreview"
            class="fixed z-900 top-0 left-0 bg-zinc-900/20 w-screen h-screen flex justify-center items-center">
            <slot />
        </div>
    </Transition>
</template>