<template>
    <div class="modal is-active">
        <div class="modal-background"></div>
            <div class="modal-content">
                <slot></slot>
            </div>
        <slot name="close"></slot>
    </div>
</template>

<script>
export default {

}
</script>
<style>

</style>