<template>
    <div>
        <div v-if="contentData.length > 0" class="block">
            <ul class="block__header">
                <li v-bind:class="headerClass" v-for="(content, index) in contentData" v-bind:key="content.title" v-on:click="selectBlock(index)">
                    {{content.title}}
                </li>
            </ul>
            <ul class="block__content">
                <li class="block__content-first-item">
                    <input type="text" placeholder="Фильтр" v-model="search">
                    <select v-model="sortBy">
                        <option v-for="option in sortOptions" v-bind:value="option.value" v-bind:key="option.title">
                            {{option.title}}
                        </option>
                    </select>
                </li>
                <li class="block__content-item" v-for="item in computedList" v-bind:key="item.id" v-on:click="showInfo(item)">
                    <span class="block__content-item-name">{{item.name}}</span> <span>{{item.score}}</span>
                </li>
                <li class="block__content-last-item" v-if="currentPage > 0">
                    <span class="block__pagination-item" v-bind:class="{ active: number === currentPage }" v-for="number in pagesQuantity" v-on:click="currentPage = number" v-bind:key="number"> 
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
    name: 'ContentBlock',
    components: {
        Modal
    },
    data() {
        return {
            activeBlockIndex: 0,
            search: '',
            sortBy: 'nameUp',
            sortOptions: [
                {title: 'По имени (А-Я)', value: 'nameUp'},
                {title: 'По имени (Я-А)', value: 'nameDown'},
                {title: 'По рейтингу (по убыванию)', value: 'ratingUp'},
                {title: 'По рейтингу (по возростанию)', value: 'ratingDown'}
            ],
            currentItem: {},
            showModal: false,
            pagesQuantity: 0,
            currentPage: 0,
        }
    },
    props: ['contentData'],
    mounted() {
        Bus.$on('closeModal', () => {
            this.showModal = false
        })
    },
    methods: {
        selectBlock(index) {
            this.activeBlockIndex = index
        },
        showInfo(itemData) {
            this.showModal = true
            this.currentItem = itemData
        },
        sortByField(field, isString) {
            if (isString)
                return (a, b) => a[field].toLowerCase() >= b[field].toLowerCase() ? 1 : -1;
            else
                return (a, b) => a[field] >= b[field] ? 1 : -1;
        },
        getFilterList() {
            let selectedContentData = this.contentData[this.activeBlockIndex]
            return selectedContentData.items.filter(item => item.name.toLowerCase().includes(this.search.toLowerCase()))
        },
        getSortedList(list) {
            if (this.sortBy === 'nameUp' || this.sortBy === 'nameDown') {
                if (this.sortBy === 'nameDown')
                    list.sort(this.sortByField('name', true)).reverse()
                else
                    list.sort(this.sortByField('name', true))
            } else {
                if (this.sortBy === 'ratingDown')
                    list.sort(this.sortByField('score'))
                else
                    list.sort(this.sortByField('score')).reverse()
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
        },
        headerClass() {
            return  {
                'block__header-item': this.contentData.length > 1,
                'block__header-single-item': this.contentData.length <= 1
            }
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

.block
    width 425px
    margin-top 10px

    &__header
        height 30px
        text-align center
        padding 0
        margin 0
        display flex
        justify-content space-around

    &__header-single-item
        list-style-type none
        padding 5px 0px
        margin-right auto


    &__header-item
        list-style-type none
        padding 5px 10px
        border 1px solid black
        border-bottom none
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
