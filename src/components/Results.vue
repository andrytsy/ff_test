<template>
    <div>
        <div v-if="results.length > 0" class="results">
            <ul v-if="results.length > 1" class="results__header">
                <li class="results__header-item" v-for="result in results" v-bind:key="result.text" v-on:click="selectResult(result)">
                    {{result.text}}
                </li>
            </ul>
            <ul class="results__content">
                <li class="results__content-first-item">
                    <input type="text" placeholder="Фильтр" v-model="search">
                    <select v-model="sortBy">
                        <option v-for="option in sortOptions" v-bind:value="option.value" v-bind:key="option.title">
                            {{option.title}}
                        </option>
                    </select>
                </li>
                <li class="results__content-item" v-for="item in computedList" v-bind:key="item.id" v-on:click="showInfo(item)">
                    <span class="results__content-item-name">{{item.name}}</span> <span>{{item.score}}</span>
                </li>
                <li class="results__content-last-item" v-if="currentPage > 0">
                    <span class="results__pagination-item" v-bind:class="{ active: number === currentPage }" v-for="number in pagesQuantity" v-on:click="currentPage = number"> 
                        {{number}}
                    </span>
                </li>
            </ul>
        </div>
        <div v-if="showModal">
            <Modal :data="currentItem" />
        </div>
    </div>
</template>

<script>
import Bus from '../utils/bus.js'
import Modal from './Modal.vue'

export default {
    name: 'Results',
    components: {
        Modal
    },
    data() {
        return {
            results: [],
            content: {},
            search: '',
            sortBy: 'nameUp',
            sortOptions: [
                {title: 'По имени (А-Я)', value: 'nameUp'},
                {title: 'По имени (Я-А)', value: 'nameDown'},
                {title: 'По рейтингу (по убыванию)', value: 'ratingUp'},
                {title: 'По рейтингу (по возростанию)', value: 'ratingDown'}
            ],
            lastSort: '',
            currentItem: {},
            showModal: false,
            pagesQuantity: 0,
            currentPage: 0,
            isMobile: false
        }
    },
    mounted() {
        Bus.$on('addToResults', (data) => {
            if (this.results.length <= 2) {
                this.results.push(data)
            } else {
                this.results.shift()
                this.results.push(data)
            }

            if (this.content.text === undefined)
                this.content = data
        })

        Bus.$on('closeModal', () => {
            this.showModal = false
        })

        if (/Android|webOS|iPhone|iPad|iPod|BlackBerry|BB|PlayBook|IEMobile|Windows Phone|Kindle|Silk|Opera Mini/i.test(navigator.userAgent))
            this.isMobile = true
            console.log(this.isMobile)
    },
    methods: {
        selectResult(result) {
            this.content = result
        },
        showInfo(itemData) {
            this.showModal = true
            this.currentItem = itemData
        },
        getFilterList() {
            return this.content.items.filter(item => {
                return item.name.toLowerCase().includes(this.search.toLowerCase())
            })
        },
        getSortedList(list) {
            if (this.sortBy === 'nameUp' || this.sortBy === 'nameDown') {
                if (this.sortBy === 'nameDown') {
                    list.sort((a, b) => {
                        if (a.name.toLowerCase() < b.name.toLowerCase())
                            return 1
                        if (a.name.toLowerCase() > b.name.toLowerCase())
                            return -1
                        return 0
                    })
                } else {
                    list.sort((a, b) => {
                        if (a.name.toLowerCase() > b.name.toLowerCase())
                            return 1
                        if (a.name.toLowerCase() < b.name.toLowerCase())
                            return -1
                        return 0
                    })
                }
            } else {
                if (this.sortBy === 'ratingDown')
                    list.sort((a, b) => a.score - b.score)
                else
                    list.sort((a, b) => b.score - a.score)
            }

            return list
        },
        getPaginatedList(list) {
            if (list.length > 10) {
                if (this.currentPage === 0)
                    this.currentPage = 1

                this.pagesQuantity = Math.ceil(list.length / 10)
                let to = this.currentPage * 10
                let from = to - 10
                return list.slice(from, to)
            }

            this.currentPage = 0
            return list
        }
    },
    computed: {
        computedList() {
            let filteredList = this.getFilterList()
            let sortedList = this.getSortedList(filteredList)
            let result = this.getPaginatedList(sortedList)

            return result
        }
    }
}
</script>


<style lang="stylus" scoped>
border-top-left-radius(n)
    -webkit-border-top-left-radius n
    -moz-border-top-left-radius n
    border-top-left-radius n

border-top-right-radius(n)
    -webkit-border-top-right-radius n
    -moz-border-top-right-radius n
    border-top-right-radius n

.results
    width 425px
    margin-top 10px

    &__header
        height 30px
        text-align center
        padding 0
        margin 0
        display flex
        justify-content space-around

    &__header-item
        list-style-type none
        padding 5px 10px
        border 1px solid black
        border-top-left-radius(10px)
        border-top-right-radius(10px)
        flex-grow 1
        cursor pointer
        text-overflow ellipsis
        white-space nowrap
        overflow hidden
        &:hover
            background-color #eee

    &__content
        border 1px solid black
        padding 0
        margin 0
        min-height 30px

    &__content-first-item
        padding 10px 5px 5px
        list-style-type none
        display flex
        justify-content space-between
    
    &__content-last-item
        padding 5px 5px 10px
        list-style-type none
        display flex
        justify-content center

    &__content-item
        padding 5px 5px
        list-style-type none
        display flex
        justify-content space-between
        cursor pointer
        &:hover
            background #eee

    &__content-item-name
        text-overflow ellipsis
        white-space nowrap
        overflow hidden

    &__pagination-item
        padding 5px 10px
        margin 0px 2px
        border 1px solid black
        cursor pointer
        &:hover
            background #eee

.active 
    background #eee
</style>
