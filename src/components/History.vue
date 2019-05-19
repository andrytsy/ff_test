<template>
    <div class="history">
        <span class="history__title" v-on:click="toggleHistory = !toggleHistory">История запросов</span>
        <transition name="expand">
            <ul v-if="toggleHistory" class="history__list">
                <li class="history__list-item" v-for="(request, index) in historyList" v-bind:key="request.text">
                    {{index + 1}}. {{request.text}} {{request.time}} <span>{{request.count}}</span>
                </li>
            </ul>
        </transition>
    </div>
</template>

<script>
import Bus from '../utils/bus.js'

export default {
    data() {
        return {
            historyList: [],
            toggleHistory: false
        }
    },
    mounted() {
        Bus.$on('addToHistory', (historyItem) => {
            this.historyList.push(historyItem)
        })
    }
}
</script>


<style lang="stylus" scoped>
transition(n)
    -webkit-transition n
    -moz-transition n
    transition n
    
text-decoration()
    -webkit-text-decoration n
    -moz-text-decoration n
    text-decoration n

.history
    margin-top 30px
    width 425px
    display flex
    flex-direction column

    &__title
        text-decoration underline
        cursor pointer
    
    &__list
        height 150px
        margin-top 10px
        margin-bottom 0px
        padding 0px
        border 1px solid black
        background-color #eee
        overflow-x hidden
    
    &__list-item
        list-style-type none
        padding 5px 10px 0px
        display flex
        justify-content space-between

.expand-enter-active, .expand-leave-active
    transition(.5s)
    height 150px
    overflow hidden

.expand-enter, .expand-leave-to
    height 0px
    overflow hidden

</style>
