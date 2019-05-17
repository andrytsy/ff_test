<template>
  <div class="wrapper" v-on:click="close">
      <div class="modal">
            <h2>{{this.data.name}}</h2>
            <p>Дата создания: {{new Date(this.data.created_at).toLocaleDateString()}}</p>
            <p>Рейтинг: {{this.data.score}}</p>
            <p>URL: <a target="_blank" :href="this.data.svn_url">{{this.data.svn_url}}</a></p>
            <hr>
            <a target="_blank" :href="this.data.owner.html_url"><p>Автор: {{this.data.owner.login}}</p></a>
            <img :src="this.data.owner.avatar_url" alt="Github Аватар" height="200" width="200">
      </div>
  </div>
</template>

<script>
import Bus from '../utils/bus.js'

export default {
    name: 'Modal',
    props: {
        data: Object
    },
    methods: {
        close() {
            Bus.$emit('closeModal')
        }
    }
}
</script>

<style lang="stylus" scoped>
.wrapper
    position fixed
    top 0
    left 0
    width 100%
    height 100%
    background-color rgba(0, 0, 0, 0.2)
    display flex
    justify-content center
    align-items center
    cursor pointer

.modal
    width 425px
    min-height 250px
    background-color #fff
    text-align center
    margin 0 auto
    cursor default
</style>
