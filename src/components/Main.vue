<template>
    <div class="wrapper">
        <Search />
        <History />
        <div v-if="isMobile === false">
            <ContentBlock :contentData="contentData" />
        </div>
        <div v-if="isMobile === true">
            <div v-for="content in contentData" v-bind:key="content">
                <ContentBlock :contentData="[content]" />
            </div>
        </div>
    </div>
</template>

<script>
import Bus from '../utils/bus.js'
import Search from './Search.vue'
import History from './History.vue'
import ContentBlock from './ContentBlock.vue'

export default {
    name: 'Main-page',
    components: {
        Search,
        History,
        ContentBlock
    },
    data() {
        return {
            contentData: [],
            isMobile: false
        }
    },
    mounted() {
        Bus.$on('addContent', (data) => {
            if (this.contentData.length <= 2) {
                this.contentData.push(data)
            } else {
                this.contentData.shift()
                this.contentData.push(data)
            }
        })

        if (/Android|webOS|iPhone|iPad|iPod|BlackBerry|BB|PlayBook|IEMobile|Windows Phone|Kindle|Silk|Opera Mini/i.test(navigator.userAgent))
            this.isMobile = true
    },
}
</script>

<style lang="stylus">
.wrapper
    width 1000px
    margin 0 auto
    text-align left 
    display flex
    flex-direction column
    align-items center
</style>
