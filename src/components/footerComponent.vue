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
    <div class="footer">
        <div class="content">
            <p> &copy; 2023 Qnaire </p>
        </div>
    </div>
</template>

<style lang="scss" scoped>
@import '../assets/color';
    .footer{
        .content{
            font-size: 1.5vh;
            color: #374747;
            display: flex;
            justify-content: center;
            align-items: center;
            text-align: center;
            height: 10vh;
            width: 100%;
            background-color: $navFooter;
        }
    }
</style>