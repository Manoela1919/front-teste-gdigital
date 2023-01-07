<template>
    <div id="menu">
        <div>
            <img v-b-modal.modal id="logo" src="@/assets/gift.svg" alt="">
            <div id="others">
                <img src="@/assets/academy.svg" alt="">
                <img src="@/assets/trophy.svg" alt="">
                <img src="@/assets/user.svg" alt="">
                <img src="@/assets/messages.svg" alt="">
            </div>
            <img src="@/assets/logo.svg" alt="">
        </div>
    </div>
</template>

<style>
    #menu {
        height: 100vh;
        width: 6.4vw;
        display: flex;
        border-right: 1.5px solid rgba(0, 0, 0, 0.185);
        flex-wrap: wrap;
        flex-direction: column;
        justify-content: space-around;
        align-items: center;
        position: absolute;
        left: 0;
        top: 0;
    }

    #others {
        align-items: center;
        align-content: center;
        display: flex;
        flex-wrap: wrap;
        flex-direction: column;
        justify-content: space-around;
        gap: 8vh;
        margin-top: 14vh;
        margin-bottom: 22vh;
    }

    img {
        width: 2vw;
    }
</style>

<script>
</script>