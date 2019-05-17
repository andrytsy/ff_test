<template>
    <span class="search">
        <input type="text" class="search__field" placeholder="Введите что-нибудь" v-model="requestText">
        <p class="search__required-error" v-if="showError">Введите что-нибудь</p>
        <input type="button" class="search__btn" value="Отправить" @click="send" :disabled="isLoading">
    </span>
</template>

<script>
import axios from 'axios'
import Bus from '../utils/bus.js'

export default {
    name: 'Search',
    data() {
        return {
            requestText: '',
            showError: false,
            isLoading: false 
        }
    },
    methods: {
        send() {
            if (this.requestText.length) {
                this.isLoading = true
                this.showError = false

                axios
                    .get('https://api.github.com/search/repositories?q=' + this.requestText)
                    .then(response => {
                        if (response.status === 200 && response.data) {
                            let date = new Date()
                            let historyRequest = {
                                text: this.requestText,
                                time: (date.getHours() < 10 ? '0' + date.getHours() : date.getHours()) + ':' + (date.getMinutes() < 10 ? '0' + date.getMinutes() : date.getMinutes()),
                                count: response.data.total_count
                            }

                            this.isLoading = false
                            response.data.title = this.requestText

                            Bus.$emit('addToHistory', historyRequest)
                            Bus.$emit('addContent', response.data)

                            this.requestText = ''
                        } else {
                            throw new Error("Houston we have a problem.")
                        }
                    })
            } else {
                this.showError = true
            }
        }
    }
}
</script>

<style lang="stylus" scoped>
.search
    max-width 365px
    padding 30px
    border 1px solid black
    display flex

    &__field
        height 36px
        width 250px
        font-size 16px
        margin-right 30px
        border 1px solid black
    
    &__required-error
        color red
        position absolute
        margin-top 40px

    &__btn
        padding 5px
        outline 0
        height 40px
        font-size 16px
        background #eee
        border 1px solid black
        &:hover
            cursor pointer
            background #e0e0e0
</style>
