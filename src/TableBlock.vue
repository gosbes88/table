<template>
    <table :class="tableClass" :style="options">
        <thead v-if="showHeader" :class="tableHeadClass">
            <tr>
                <th class="cell-index"
                    :class="tableHeadCellClass"
                    v-if="showIndex"
                    v-text="'№'"
                ></th>
                <th :class="tableHeadCellClass"
                    v-for="column in columns"
                    v-text="column.label || column.prop"
                ></th>
            </tr>
        </thead>
        <tbody :class="tableBodyClass">
            <template v-if="data.length">
                <tr v-for="(item, index) in data" :key="item.id">
                    <td class="cell-index"
                        :class="tableBodyCellClass"
                        v-if="showIndex"
                        v-text="index + 1"
                    ></td>
                    <td :class="tableBodyCellClass"
                        v-for="(column, index) in columns"
                        :key="index"
                        :style="column.options"
                    >
                        <slot :name="column.prop" :data="item">
                            <div :title="item[column.prop] || ''">{{item[column.prop] || ''}}</div>
                        </slot>
                    </td>
                </tr>
            </template>
            <tr v-else>
                <td :colspan="columns.length + showIndex" :class="tableEmptyClass" v-text="'Empty content'"></td>
            </tr>
        </tbody>
    </table>
</template>

<script>
    export default {
        name: "table-block",
        props: {
            data: {
                type: Array,
                default: () => []
            },
            options: {
                type: Object,
                default: () => ({})
            },
            /**
             * {prop, label, options(css options)}
             */
            columns: {
                type: Array,
                required: true,
                validator(val) {
                    return val.every(el => el.prop);
                }
            },
            showIndex: {
                type: Boolean,
                default: true
            },
            showHeader: {
                type: Boolean,
                default: true
            },
            tableClass: {
                type: String,
                default: 'table'
            },
            tableHeadClass: {
                type: String,
                default: 'table__head'
            },
            tableBodyClass: {
                type: String,
                default: 'table__body'
            },
            tableHeadCellClass: {
                type: String,
                default: 'table__head--cell'
            },
            tableBodyCellClass: {
                type: String,
                default: 'table__body--cell'
            },
            tableEmptyClass: {
                type: String,
                default: 'table-empty'
            }
        }
    };
</script>

<style lang="scss" scoped>
    .table {
        border-collapse: collapse;
        border: 1px;
        cursor: default;
        user-select: none;

        &__head {
            text-transform: uppercase;
        }

        &__head--cell {
            text-align: center;
            background: aquamarine;
            padding: 10px 0;
        }

        &__body--cell {
            text-align: left;
            vertical-align: middle;
            padding: 10px;
            border-bottom: 1px solid #ccc;

            div {
                width: 100%;
                white-space: nowrap;
                overflow: hidden;
                text-overflow: ellipsis;
            }
        }

        tr:nth-child(even) {
            background: #ccc;
        }
    }

    .cell-index {
        width: 40px;
        overflow: hidden;
    }

    .table-empty {
        text-align: center;
        padding: 10px 0;
        color: #ccc;
    }
</style>
