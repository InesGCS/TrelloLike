<template>
<div class="popUp">
    <div class='popUpInner'>
        <slot />
        <button class="popUp-close" @click="TogglePopUp('buttonTrigger')">
            Close PopUp
        </button>
    </div>
</div>
</template>

<script>
export default {
  props: ['TogglePopUp']
}

</script>

<style>
.popUp {
    position:fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    z-index: 99;
    background-color: rgba(0, 0, 0, 0.2);

    display: flex;
    align-items: center;
    justify-content: center;
}

.popUpInner {
    background: #fff;
    padding: 32px;
}
</style>
