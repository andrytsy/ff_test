<template>
    <div class="wrapper">
        
        <Search />
        <History />
        <Results />
    </div>
</template>

<script>
import Search from './Search.vue'
import History from './History.vue'
import Results from './Results.vue'

export default {
    name: 'Main-page',
    components: {
        Search,
        History,
        Results
    }
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
