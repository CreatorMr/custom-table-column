<!--
 * @@author: Creator
 * @@Email:
 * @LastEditTime:
 * @Description:
-->

<template>
    <div class="setting-column" :class="className">
        <div v-if="isArrow" class="arrow"></div>
        <div v-if="title" class="title">{{ title }}</div>
        <div class="dragWrap">
            <draggable v-model="model" :options="options">
                <div class="column-item" v-for="(item, index) in columnListAll" :key="item.key" :class="itemDraggable(item.disabled)">
                    <div class="info">
                        <el-checkbox v-model="item.selected" @change="val => checkboxChange(index, val)" :disabled="item.disabled">
                            {{ item.title }}
                        </el-checkbox>
                    </div>
                    <div class="sort">
                        <div class>
                            <span @click="changeDraggable(index, item)">
                                <el-tooltip :placement="index === 0 ? 'left' : 'top'" content="固定列">
                                    <img v-if="!item.disabled" class="lock" :src="unlockImg" alt />
                                    <img v-else class="lock" :src="lockActiveImg" alt />
                                </el-tooltip>
                            </span>
                            <el-tooltip :placement="index === 0 ? 'left' : 'top'" content="拖拽手柄">
                                <img class="drag" :src="dragThreeImg" alt />
                            </el-tooltip>
                        </div>
                        <div v-if="openSingleOperate">
                            <i class="el-icon-caret-top" @click="dropSort(index, true)"></i>
                            <i class="el-icon-caret-bottom" @click="dropSort(index, false)"></i>
                        </div>
                    </div>
                </div>
            </draggable>
        </div>
        <div class="el-btn">
            <el-button size="large" type="primary" @click="ok">确认</el-button>
            <el-button size="large" type="default" class="chancel" @click="chancel">取消</el-button>
        </div>
    </div>
</template>

<script>
import draggable from 'vuedraggable'
import cloneDeep from 'lodash/cloneDeep'
import dragThreeImg from './images/drag-three.png'
import lockActiveImg from './images/lock_active.png'
import unlockImg from './images/unlock_grey.png'
export default {
    name: 'SettingColumn',
    components: {
        draggable,
    },
    data() {
        return {
            dragThreeImg,
            lockActiveImg,
            unlockImg,
            list: '',
        }
    },
    props: {
        title: {
            type: String,
            default: '',
        },
        //
        columnListAll: {
            type: Array,
            default: () => {
                return []
            },
        },
        // 是否禁止拖动
        forbidDrag: {
            type: Boolean,
            default: false,
        },
        className: {
            type: String,
            default: '',
        },
        // 是否开启单点操作,上移、下移
        openSingleOperate: {
            type: Boolean,
            default: false,
        },
        isArrow: {
            type: Boolean,
            default: false,
        },
    },
    computed: {
        model: {
            get() {
                return this.columnListAll
                // return this.columnListAll.map(x => ({ ...x, disabled: true }))
            },
            set(value) {
                let list = cloneDeep(this.columnListAll)
                list = value
                this.list = list
                // console.log(value, 'value')
                this.$emit('onDraggable', value)
            },
        },
        options() {
            return {
                disabled: this.forbidDrag,
                draggable: '.darggable',
            }
        },
        itemDraggable() {
            return drag => {
                return !drag ? 'darggable' : ''
            }
        },
    },
    mounted() {},
    methods: {
        dropSort(index, isUp = true) {
            const list = cloneDeep(this.columnListAll)
            if (isUp) {
                if (index === 0) return
                let temp = list.splice(index, 1)
                list.splice(index - 1, 0, temp[0])
            } else {
                if (index === list.length - 1) return
                let temp = list.splice(index, 1)
                list.splice(index + 1, 0, temp[0])
            }
            this.$emit('onDraggable', list)
        },
        checkboxChange(index, value) {
            // if (index === 0 || index === 1) return
            const list = cloneDeep(this.columnListAll)
            console.log(this.columnListAll, 'this.columnListAll')
            list[index].selected = value
            this.list = list
            //  this.$emit('onChange', list)
        },
        changeDraggable(index, item) {
            console.log(index, 'index')
            console.log(item)
            const list = cloneDeep(this.columnListAll)
            list[index].disabled = !item.disabled
            this.list = list
            this.$emit('onDraggable', this.list)
        },
        ok() {
            this.$emit('onChange', this.list, false)
        },
        chancel() {
            this.$emit('onChancel', false)
        },
    },
}
</script>

<style lang="scss" scoped>
.customPosition {
    border: 1px solid #e9e9e9;
    border-radius: 16px;
    padding: 20px;
    box-shadow: 0 0 8px 0 hsla(0, 0%, 75%, 0.5);
}
.setting-column {
    display: flex;
    flex-direction: column;
    max-height: 400px;
    background: #ffffff;
    position: relative;
    z-index: 1000;
    .title {
        font-size: 24px;
        padding: 15px;
        font-weight: bold;
        border-bottom: 1px solid #ccc;
    }
    .dragWrap {
        flex: 1;
        overflow-y: scroll;
    }
    .column-item {
        display: flex;
        justify-content: space-between;
        height: 46px;
        padding: 0 10px;
        background: #f2f4f6;
        margin: 5px 0;
        border-radius: 4px;
        .info {
            margin-right: 30px;
            display: flex;
            align-items: center;
            justify-content: center;
        }
    }
    .sort {
        display: flex;
        align-items: center;
        img {
            width: 20px;
            height: 20px;
            margin-left: 20px;
        }
    }
    .arrow {
        display: block;
        width: 0;
        height: 0;
        position: absolute;
        border-color: transparent;
        border-style: solid;
        top: -70px;
        right: 70px;
    }
    .arrow::after {
        content: ' ';
        top: 1px;
        margin-left: -7px;
        border: 20px solid transparent;
        border-bottom: 20px solid #fff;
    }
}
.el-btn {
    text-align: right;
    margin-top: 10px;
}
.chancel {
    margin-left: 10px;
}
</style>
>
