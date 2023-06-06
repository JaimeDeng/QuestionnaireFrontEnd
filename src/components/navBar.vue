<script>
import slideWindow from './slideWindow.vue';
export default (await import('vue')).defineComponent({
components: {
    slideWindow
},
data() {
    return{
    }
},
methods: {
    toHome(){
        this.$router.push('/');
    },
    slideWindow(){
        const slideWindow = document.getElementById("slideWindow");
        slideWindow.style.transform = "translateX(0)";
    },
    closeWindow(){
        const slideWindow = document.getElementById("slideWindow");
        slideWindow.style.transform = "translateX(120%)";
    }
},
mounted() {

}
})
</script>

<template>
    <slideWindow @closeWin="closeWindow" class="slideWindow" id="slideWindow" />
    <nav>
        <div @click="toHome" class="logo">
            <font-awesome-icon class="fa-check-to-slot" icon="fa-solid fa-check-to-slot" />
            <h1 class="title">Qnaire</h1>
        </div>
        <h5 @click="slideWindow" class="signIn">Sign in</h5>
    </nav>
</template>

<style lang="scss" scoped>
@import '../assets/color';
    .slideWindow{
        position: absolute;
        transform: translateX(120%);
        transition: 1s;
    }
    nav{
        position: relative;
        width: 100vw;
        height: 10%;
        background-color: $page;
        .logo{
            cursor: pointer;
            position: relative;
            width: max-content;
            .fa-check-to-slot{
                margin-top: 1vh;
                margin-left: 1vw;
                font-size: 4.5vh;
                transform: rotate(-7deg);
                color: $navFooter;
            }
            .title{
                margin-left: 0.1vw;
                display: inline;
                font-size: 3.5vh;
                color: $navFooter;
                font-family: "adobe heiti std";
            }
        }
        .signIn{
            font-size: 2vh;
            color: #374747;
            cursor: pointer;
            position: absolute;
            right: 2%;
            top: 20%;
            transition: 0.4s;

            &:hover{
                color: rgb(177, 199, 199);
            }
        }

    }
</style>