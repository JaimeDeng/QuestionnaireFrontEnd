<script>
export default (await import('vue')).defineComponent({
components: {

},
data() {
    return{
    }
},
methods: {
    toHome(){
        this.$router.push('/');
    }
},
mounted() {

}
})
</script>

<template>
    <nav>
        <div @click="toHome" class="logo">
            <font-awesome-icon class="fa-check-to-slot" icon="fa-solid fa-check-to-slot" />
            <h1 class="title">Qnaire</h1>
        </div>
    </nav>
</template>

<style lang="scss" scoped>
@import '../assets/color';
    nav{
        position: relative;
        width: 100vw;
        height: 10vh;
        .logo{
            cursor: pointer;
            position: relative;
            .fa-check-to-slot{
                margin-top: 1vh;
                margin-left: 1vw;
                font-size: 7vh;
                transform: rotate(-7deg);
                color: $navFooter;
            }
            .title{
                margin-left: 0.1vw;
                display: inline;
                font-size: 5vh;
                color: $navFooter;
                font-family: "adobe heiti std";
            }
        }

    }
</style>