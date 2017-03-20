<template>
    <div style="width:100%;height:100%;" class="layui-tab-brief">
        <ul class="layui-tab-title site-demo-title" style="margin-bottom:10px;">
            <li class="layui-this">人民币转换</li>
            <li @click="$options.filters.openRedirect('http://coding.net/u/yimocoding/p/metools/git/blob/master/src/pages/rmbconvert.vue')">查看代码</li>
        </ul>
        <div style="width:100%;height:100%;">   
            <div class="layui-form-item layui-form-text">
                <label class="layui-form-label">处理前</label>
                <div class="layui-input-block">
                    <input v-model="beforeTxt" placeholder="数字..." class="layui-input"/>
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
                <label class="layui-form-label">处理后</label>
                <div class="layui-input-block">
                    <textarea v-model="afterTxt" placeholder="中文..." class="layui-textarea"></textarea>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    import rmbHelper from '../utils/rmbHelper'
    export default {
        data() {
            return {
                beforeTxt:'',
                afterTxt:'',
            }
        },
        mounted(){
        },
        methods: {
            convertDxRmb(){
                var beforeTxt=this.beforeTxt;
                this.afterTxt=rmbHelper.convert(beforeTxt);
            },
            convertXxRmb(){
                var afterTxt=this.afterTxt;
                this.beforeTxt=rmbHelper.rmbToNum(afterTxt);
            }
        }
    }

</script>