<script>
export default (await import('vue')).defineComponent({
components: {

},
data() {
    return{
    }
},
methods: {
},
mounted() {

}
})
</script>

<template>
    <footer>
        <p> &copy; 2023 Qnaire </p>
    </footer>
</template>

<style lang="scss" scoped>
@import '../assets/color';
    footer{
        position: relative;
        display: flex;
        text-align: center;
        justify-content: center;
        align-items: center;
        height: 10vh;
        width: 100vw;
        background-color: $navFooter;
    }
</style>