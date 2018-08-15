<template>
    <v-tab :items="tabItems">
        <div slot="main">
            <div class="layui-form-item layui-form-text">
                <label class="layui-form-label">当前时间</label>
                <div class="layui-input-block">
                    <input v-model="currentTime" placeholder="当前时间..." class="layui-input"/>
                </div>
            </div>
            <div class="layui-form-item layui-form-text">
                <label class="layui-form-label">当前时间戳</label>
                <div class="layui-input-block">
                    <input v-model="currentTimestamp" placeholder="当前时间戳..." class="layui-input"/>
                </div>
            </div>
            <div class="layui-form-item layui-form-text">
                <label class="layui-form-label">输入</label>
                <div class="layui-input-block">
                    <input v-model="timeTxt" @click="pickTime(timeTxtId)" :id="timeTxtId" placeholder="时间或时间戳..." class="layui-input"/>
                </div>
            </div>

            <div class="layui-form-item layui-form-text">
                <div class="layui-input-block">
                    <v-button @click="convertDxRmb()">
                        <span>大写 <i class="layui-icon">&#xe61a; </i></span>
                    </v-button>
                    <v-button @click="convertXxRmb()">
                        <span>小写 <i class="layui-icon">&#xe619; </i></span>
                    </v-button>
                </div>
            </div>
            <div class="layui-form-item layui-form-text">
                <label class="layui-form-label">输出</label>
                <div class="layui-input-block">
                    <input v-model="timestampTxt" @change="inputTimestamp(timestampTxt)" placeholder="中文..." class="layui-input" />
                </div>
            </div>
        </div>
    </v-tab>
</template>
<script>
    import rmbHelper from '../utils/rmbHelper'
    import moment from 'moment'
    export default {
        data() {
            return {
                tabItems:[
                    {
                        Name:'main',
                        Title:'时间与时间戳'
                    }
                ],
                currentTime: moment().format('YYYY-MM-DD HH:mm:ss'),
                currentTimestamp: moment().format('X'),
                timeTxt:'',
                timestampTxt:'',
                timeTxtId:'timeTxt',
            }
        },
        mounted(){
        },
        created: function () {
            setInterval(this.timer, 1000)
        },
        methods: {
            convertDxRmb(){
                var beforeTxt=this.beforeTxt;
                this.afterTxt=rmbHelper.convert(beforeTxt);
            },
            convertXxRmb(){
                var afterTxt=this.afterTxt;
                this.beforeTxt=rmbHelper.rmbToNum(afterTxt);
            },
            timer() {
                this.currentTime = moment().format('YYYY-MM-DD HH:mm:ss')
                this.currentTimestamp = moment().format('X')
            },
            pickTime(id) {
                layui.laydate({
                    elem: document.getElementById(id),
                    format: this.dateFormat||'YYYY-MM-DD mm:mm:ss',
                    choose: (dates) => {
                        this.timeTxt = dates
                        this.timestampTxt = moment(dates).format('X')
                    }
                })
            },
            inputTimestamp(timestamp) {
                this.timeTxt = !isNaN(timestamp) ? moment.unix(timestamp).format('YYYY-MM-DD HH:mm:ss') : ''
            }

        }
    }

</script>