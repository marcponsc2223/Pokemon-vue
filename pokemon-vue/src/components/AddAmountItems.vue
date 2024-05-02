<template>
    <div class="button-container divAddAndDelete">
        <button class="btn btn-Icons"><img src="../assets/lessIcon.png" alt="LessIcon.png" class="imgs" @click="amountGestion(this.remove)"></button>
        <button class="btn btn-Icons"><img src="../assets/addIcon.png" alt="AddIcon.png" class="imgs" @click="amountGestion(this.add)"></button>
    </div>
</template>
<script>
    export default {
        data() {
            return {
                remove: 'remove',
                add: 'add',
                addingItem: false,

            }
        },
        methods: {
            amountGestion(ord) {
                ord === 'add' ? this.addingItem = true : this.addingItem = false
                this.$emit('amountGestion', this.addingItem);
            }
        },
    }
</script>
<style>
    .divAddAndDelete {
        border-radius: 18px;
        border: 1px solid #00000069;
        display: flex;
        justify-content: center;
    }
    .btn-Icons {
        transition: 0.2s;
    }
    .btn-Icons:hover{
        transform: scale(1.03);
    }
    .imgs {
        width: 28px;
    }
</style>