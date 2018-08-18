<template>
    <v-tab :items="tabItems">
        <div slot="fanyi">
            <div class="layui-form-item layui-form-text">
                <label class="layui-form-label">翻译的句子</label>
                <div class="layui-input-block">
                    <textarea v-model="model.beforeTxt" @keydown.enter="fanyi"
                              placeholder="请输入转换前字符串..."
                              class="layui-textarea"></textarea>
                    <button class="layui-btn copy-btn" type="button" :class="{ 'layui-btn-disabled': !model.beforeTxt }"
                            v-clipboard:copy="model.beforeTxt"
                            v-clipboard:success="onCopy"
                            v-clipboard:error="onError"><i class="layui-icon layui-icon-templeate-1">&#xe630;</i></button>
                </div>
            </div>
            <div class="layui-form-item layui-form-text">
                <label class="layui-form-label">翻译</label>
                <div class="layui-input-inline">
                    <v-select :options="language.options" :value="language.currentValue" style="float: left;margin-right:10px;" @input="currentLanguageHandler"></v-select>
                    <v-select :options="way.options" :value="way.currentValue" style="float: left;" @input="currentWayHandler"></v-select>
                </div>
            </div>
            <div class="layui-form-item layui-form-text">
                <label class="layui-form-label">翻译结果</label>
                <div class="layui-input-block">
                    <textarea v-model="model.afterTxt"
                              placeholder="翻译结果..."
                              class="layui-textarea"></textarea>
                    <button class="layui-btn copy-btn" type="button" :class="{ 'layui-btn-disabled': !model.beforeTxt }"
                            v-clipboard:copy="model.beforeTxt"
                            v-clipboard:success="onCopy"
                            v-clipboard:error="onError"><i class="layui-icon layui-icon-templeate-1">&#xe630;</i></button>
                </div>
            </div>
            <div class="layui-form-item layui-form-text">
                <pre v-html="model.jsonData" v-if='model.jsonData' class="layui-code">
                </pre>
            </div>
        </div>
    </v-tab>
</template>
<script>
import common from '../utils/common'
import CryptoJS from 'crypto-js'
export default {
    data() {
        return {
            language: {
                options: [{
                    Value: 'zh',
                    Text: '汉语'

                },{
                    Value: 'en',
                    Text: '英语'
                }],
                currentValue: ''
            },
            way: {
                options: [{
                    Value: 'Baidu',
                    Text: '百度翻译'

                },{
                    Value: 'Youdao',
                    Text: '有道翻译'
                }],
                currentValue: ''
            },
            api: {
                'Baidu': {
                    url: 'api.fanyi.baidu.com/api/trans/vip/translate',
                    app_id: '20180611000174806',
                    app_secret: 's5u4COBv0vuJjYHuMeem'

                }
            },
            tabItems: [
                {
                    Name: 'fanyi',
                    Title: '在线翻译'
                },
                {
                    Name: 'code',
                    Title: '查看代码',
                    Url: 'http://coding.net/u/yimocoding/p/metools/git/blob/master/src/pages/fanyi.vue'
                }
            ],
            model: {
                beforeTxt: '',
                afterTxt: '',
                afterTxt2:'',
                jsonData:''
            },
            loading: false,
            loading2: false
        }
    },
    watch: {
        '$route': 'fetchData'
    },
    created() {
        this.fetchData()
    },
    methods: {
        fetchData() {
            this.language.currentValue = this.language.options[0].Value
            this.way.currentValue = this.way.options[0].Value
        },
        currentLanguageHandler(e){
            this.language.currentValue = e
            if(!this.model.beforeTxt) {
                this.fanyi()
            }
        },
        currentWayHandler(e){
            this.way.currentValue = e
            if(this.model.beforeTxt) {
                this.fanyi()
            }
        },
        fanyi(){
            let query = this.model.beforeTxt
            let language = this.language.currentValue
            let way = this.way.currentValue
            switch (way) {
                case 'Baidu':
                    let url = (common.getProtocol()?common.getProtocol():'http') + '://' + this.api.Baidu.url
                    let app_id = this.api.Baidu.app_id
                    let salt = (new Date).getTime()
                    let app_secret = this.api.Baidu.app_secret
                    this.loading2 = true
                    layui.jquery.ajax({
                        url: url,
                        type: 'GET',
                        dataType: 'jsonp',
                        data: {
                            q: query,
                            from: 'auto',
                            to: language,
                            appid: app_id,
                            salt: salt,
                            sign: CryptoJS['MD5'](app_id + query + salt + app_secret).toString(),
                        },
                        success: (data) => {
                            this.loading2=false;
                            this.model.afterTxt=''
                            data.trans_result.forEach(item=>{
                                this.model.afterTxt += item.dst+'\n'
                            });
                            this.model.jsonData='';
                        },
                        error: (error) => {
                            this.loading2=false;
                            this.model.afterTxt = '翻译失败~~~'
                            this.model.jsonData = JSON.stringify(data);
                        }
                    })
                break
                case 'Youdao':

                    break
            }

        },
        youdaofanyi() {
            //http://fanyi.youdao.com/openapi?path=data-mode
            let self = this;
            var str = this.model.beforeTxt;
            if (common.trim(str) == '') {
                self.model.beforeTxt = '';
                return;
            }
            var api = common.getProtocol()+'://fanyi.youdao.com/openapi.do';
            self.loading = true;
            layui.jquery.ajax({
                url:api,
                data: {
                    keyfrom:'metools',
                    key:'955743043',
                    type:'data',
                    doctype:'jsonp',
                    version:1.1,
                    q: str,
                },
                type: 'get',
                dataType: 'jsonp',
                success: function (resp) {
                    self.loading = false;
                    if(resp.errorCode==0){
                        self.model.afterTxt = '';
                        resp.translation.forEach(item=>{
                            self.model.afterTxt+=item+'\n'
                        })
                        self.model.jsonData='';
                    }
                    else{
                        self.model.afterTxt='翻译失败~~~'
                        self.model.jsonData=JSON.stringify(resp);
                    }
                },
                error:function(data){
                    self.loading=false;
                    self.model.afterTxt='翻译失败~~~'
                    self.model.jsonData=JSON.stringify(data);
                }
            })
        },
        baidufanyi(){           
            //http://api.fanyi.baidu.com/api/trans/product/apihelp
            let self=this;
            var url=common.getProtocol()=='http'?'http://api.fanyi.baidu.com/api/trans/vip/translate':'https://fanyi-api.baidu.com/api/trans/vip/translate';
            if (common.trim(self.model.beforeTxt) == '') {
                self.model.beforeTxt = '';
                return;
            }
            var appid = '20170416000044969';
            var key = 'X1ZMBUNuENgb7pzMVrpA';
            var salt = (new Date).getTime();
            var query =self.model.beforeTxt
            // 多个query可以用\n连接  如 query='apple\norange\nbanana\npear'
            var from = 'auto';
            var to = 'auto';
            var str1 = appid + query + salt +key;
            var sign = CryptoJS['MD5'](str1).toString();
            self.loading2=true;
            layui.jquery.ajax({
                url: url,
                type: 'get',
                dataType: 'jsonp',
                data: {
                    q: query,
                    appid: appid,
                    salt: salt,
                    from: from,
                    to: to,
                    sign: sign
                },
                success: function (data) {
                    self.loading2=false;
                    self.model.afterTxt2=''
                    data.trans_result.forEach(item=>{
                        self.model.afterTxt2+=item.dst+'\n'
                    });
                    self.model.jsonData='';
                },
                error:function(data){
                    self.loading2=false;
                    self.model.afterTxt2='翻译失败~~~'
                    self.model.jsonData=JSON.stringify(data);
                }
            });

        }
    }
}

</script>