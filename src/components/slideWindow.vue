<script>
export default (await import('vue')).defineComponent({
components: {

},
data() {
    return{
    }
},
methods: {
    closeWindow(){
        this.$emit('closeWin');
    }
},
mounted() {

}
})
</script>

<template>
    <div class="window">
        <h1 class="title">Sign in</h1>
        <div class="accountFrame">
            <label for="account">帳號</label>
            <font-awesome-icon icon="fa-solid fa-user" class="user" />
            <input type="text" id="account" class="account">
        </div>
        <div class="passwordFrame">
            <label for="password">密碼</label>
            <font-awesome-icon icon="fa-solid fa-key" class="key" />
            <input type="text" id="password" class="password">
        </div>
        <button @click="signIn" class="signIn">登入</button>
        <RouterLink class="signUp" to="" ><p>沒有帳號嗎?</p></RouterLink>
        <button @click="closeWindow" class="close">關閉</button>
    </div>
</template>

<style lang="scss" scoped>
@import '../assets/color';
    .window{
        position: absolute;
        right: 0;
        height: 100vh;
        width: 20vw;
        background-color: white;
        z-index: 1;
        box-shadow: 0px 0px 30px 30px rgba(104, 104, 104, 0.3);
        .title{
            margin-top: 20%;
            margin-left: 10%;
            color: $navFooter;
            font-family: "adobe heiti std";
        }

        label{
            position: absolute;
            left: -20%;
            top: 50%;
            transform: translateY(-50%);
            margin-right: 1vw;
        }
        .accountFrame{
            position: relative;
            margin: auto;
            margin-top: 3vh;
            width: max-content;
            .account{
                padding-left: 12%;
                height: 2.6vh;
                width: 10vw;
                border-radius: 0.5vh;
                border: 1px solid;
            }
            .user{
                font-size: 1.3vh;
                position: absolute;
                left: 5%;
                top: 50%;
                transform: translateY(-50%);
            }
        }
        .passwordFrame{
            position: relative;
            margin: auto;
            margin-top: 2vh;
            width: max-content;
            .password{
                padding-left: 12%;
                height: 2.6vh;
                width: 10vw;
                border-radius: 0.5vh;
                border: 1px solid;
            }
            .key{
                font-size: 1.3vh;
                position: absolute;
                left: 5%;
                top: 50%;
                transform: translateY(-50%);
            }
        }
        .signIn{
            height: 2.5vh;
            width: 3vw;
            margin-top: 5%;
            margin-left: 60%;
            background-color: $button;
            border-radius: 0.8vh;
            border: 1px solid #657c7c;
            transition: 0.4s;
            

            &:hover{
                background-color: #b3c5c5;
            }

            &:active{
                scale: 0.95;
            }
        }
        .signUp{
            width: min-content;
            font-size: 1.5vh;
            margin-top: 5%;
            margin-left: 60%;
            color: #657c7c;
            text-decoration: none;
            p{
                display: inline;
            }
        }
        .close{
            position: absolute;
            height: 3vh;
            width: 5vw;
            background-color: $button;
            border-radius: 0.8vh;
            border: 1px solid #657c7c;
            right: 10%;
            bottom: 5%;
            transition: 0.4s;
            

            &:hover{
                background-color: #b3c5c5;
            }

            &:active{
                scale: 0.95;
            }
        }
    }
</style>