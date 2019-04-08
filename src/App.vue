<template>
    <div id="app">
        <input type="search"
               v-model="filter"
               class="inp-search"
               :class="{'inp-search__active': filter.length}"
               placeholder="Search by title"
        />
        <table-block :data="data" :columns="columns">
            <div :slot="'userId'" slot-scope="props" id="detail-info">
                <a @click.stop="setVisibility(props.data.id)" href="javascript: void(0);" v-text="props.data.userId"></a>
                <div id="detail-info-tooltip"
                     v-if="visibleElements[props.data.id]"
                     @click.stop="visibleElements = {}"
                >
                    <span v-text="`ID: ${props.data.id}`"></span>
                    <span v-text="`UserId: ${props.data.userId}`"></span>
                </div>
            </div>
        </table-block>
        <v-pagination v-if="totalPages" v-model="page" :page-count="totalPages"/>
    </div>
</template>

<script>
    export default {
        name: 'app',
        data() {
            return {
                data: [],
                page: 1,
                limit: 10,
                filter: '',
                timer: null,
                totalPages: 0,
                visibleElements: {}
            };
        },
        components: {
            TableBlock: () => import('./TableBlock'),
            vPagination: () => import('vue-plain-pagination')
        },
        watch: {
            filter(val, oldVal) {
                clearTimeout(this.timer);
                this.timer = setTimeout(() => this.getTableData(), 500);
            },
            page(val) {
                this.changePage(val);
            }
        },
        created() {
            document.querySelector('body').addEventListener('click', () => this.visibleElements = {});
            this.getTableData(response => {
                console.log(response/*.headers.map.link[0]*/);
                this.data = response.body;
                this.totalPages = Math.ceil(response.headers.map['x-total-count'] / this.limit);
            });
        },
        methods: {
            setVisibility(id) {
                const oldValue = this.visibleElements[id];
                this.visibleElements = {};
                this.visibleElements[id] = !oldValue;
            },
            changePage() {
                this.getTableData(response => {
                    this.data = response.body;
                });
            },
            getTableData(callback = () => {}) {
                this.$http.get(`http://localhost:3000/posts?_page=${this.page}&_limit=${this.limit}&title_like=${this.filter}`)
                    .then(response => callback(response))
                    .catch(e => console.error(e));
            }
        },
        computed: {
            columns() {
                return [
                    {prop: 'userId', label: 'UserId', options: {'width': '80px', 'font-size': '20px'}},
                    {prop: 'title', label: 'Title', options: {'width': '200px', 'max-width': '200px'}},
                    {prop: 'body', label: 'Content', options: {'width': '400px', 'max-width': '300px'}}
                ];
            }
        }
    };
</script>

<style lang="scss">
    $borderColor: aquamarine;

    .inp-search {
        border: none;
        outline: none;
        margin-bottom: 20px;
        border-bottom: 2px solid #ccc;
        width: 700px;
        transition: all .5s;

        &:focus, &__active {
            border-bottom: 2px solid $borderColor;
        }
    }

    #detail-info, #detail-info-tooltip {
        overflow: visible;
        white-space: nowrap;
        text-overflow: unset;
        width: auto;
    }

    #detail-info-tooltip {
        position: absolute;
        top: -5px;
        left: -10px;
        background: #fff;
        border-radius: 3px;
        padding: 5px 10px;
        box-shadow: 0 0 25px 3px rgba(0,0,0,.2);
    }

    #detail-info {
        position: relative;
    }

    .pagination {
        list-style-type: none;
        padding-inline-start: 0;
        align-items: center;
        justify-content: center;
        width: 700px;
        display: flex;

        .pagination-item {
            margin-right: 10px;
        }

        .pagination-link--disable,
        .pagination-item--disable {
            pointer-events: none !important;
            filter: grayscale(0);
            cursor: not-allowed !important;
        }

        button.pagination-link {
            width: 40px;
            height: 40px;
            border-radius: 50%;
            border: none;
            outline: none;
            box-shadow: 0 0 9px 3px rgba(0, 0, 0, .1);
            cursor: pointer;
            transition: background 0.5s;
        }

        .pagination-link--active {
            background: aquamarine;
        }
    }
</style>
