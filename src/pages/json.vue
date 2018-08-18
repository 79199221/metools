<template>
    <v-tab :items="tabItems">

        <div slot="main">
            <div class="layui-form-item layui-form-text">
                <label class="layui-form-label">转换前</label>
                <div class="layui-input-block">
                    <textarea v-model="model.beforeTxt"
                              placeholder="转换前字符串..."
                              class="layui-textarea"></textarea>
                    <button class="layui-btn copy-btn" type="button" :class="{ 'layui-btn-disabled': !model.beforeTxt }"
                            v-clipboard:copy="model.beforeTxt"
                            v-clipboard:success="onCopy"
                            v-clipboard:error="onError"><i class="layui-icon layui-icon-templeate-1">&#xe630;</i></button>
                </div>
            </div>
            <div class="layui-form-item layui-form-text">
                <label class="layui-form-label">类型</label>
                <div class="layui-input-block">
                    <v-radio :optionVals="codeTypes"
                             @input="currentHandler"></v-radio>
                </div>
            </div>
            <div class="layui-form-item layui-form-text">
                <label class="layui-form-label">转换后</label>
                <div class="layui-input-block">
                    <textarea v-model="model.afterTxt"
                              placeholder="转换后字符串..."
                              class="layui-textarea"></textarea>
                    <button class="layui-btn copy-btn" type="button" :class="{ 'layui-btn-disabled': !model.afterTxt }"
                            v-clipboard:copy="model.afterTxt"
                            v-clipboard:success="onCopy"
                            v-clipboard:error="onError"><i class="layui-icon layui-icon-templeate-1">&#xe630;</i></button>
                </div>
            </div>
        </div>
    </v-tab>
</template>
<script>
    import CryptoJS from 'crypto-js'
    import common from '../utils/common'
    export default {
        data() {
            return {
                tabItems: [
                    {
                        Name: 'main',
                        Title: '编码转换'
                    }
                ],
                model: {
                    beforeTxt: '',
                    afterTxt: '',
                    currentType: ''
                },
                codeTypes: ['array', 'json','xml']
            }
        },
        watch: {
            '$route': 'fetchData'
        },
        created() {
            let str1 = 'array (1, ["a"=>"test"], "b" => [1,2,["a", "b"]] )'
            let str2 = '[ "a"=>"test", "b" => [1,2,["a", "b"]] ]'
            if(/(^array\s*\(.*\)$)|(^\[.*\]$)/.test(str1)){
                //array
                let stra = str1.match(/(^array\s*\((.*)\)$)|(^\[(.*)\]$)/i)
                let strb = str2.match(/(^array\s*\((.*)\)$)|(^\[(.*)\]$)/i)
                let str3 = stra[2] || stra[4]
                console.log(str3)
            }
        },
        methods: {
            currentHandler(item) {
                if(this.model.currentType == item) return;
                this.model.currentType = item;
                if(!this.model.beforeTxt) return;
                this.convert()

            },
            convert(){
                switch (this.model.currentType) {
                    case 'array':
                        console.log(this.model.beforeTxt)
                        // console.log(eval("("+this.model.beforeTxt+")"))
                        // console.log(JSON.parse(this.model.beforeTxt))
                        // console.log(this.model.beforeTxt)
                        break;
                    case 'json':
                        console.log(this.model.beforeTxt)
                        break;
                    default :;
                }
            },
            encode(type) {
                let self = this;
                let data = {
                    beforeTxt: this.model.beforeTxt || '',
                    afterTxt: ''
                };
                switch (type) {
                    case 'utf-8':
                        data.afterTxt = data.beforeTxt.replace(/[^\u0000-\u00FF]/g, function ($0) { return escape($0).replace(/(%u)(\w{4})/gi, "&#x$2;") });
                        break;
                    case 'ascii':
                        var character = data.beforeTxt
                        var ascii = "";
                        for (var i = 0; i < character.length; i++) {
                            var code = Number(character[i].charCodeAt(0));
                            var charAscii = code.toString(16);
                            charAscii = new String("0000").substring(charAscii.length, 4) + charAscii;
                            ascii += "\\u" + charAscii;
                        }
                        data.afterTxt = ascii;
                        break;
                    case 'unicode':
                        for (var i = 0; i < data.beforeTxt.length; i++)
                            data.afterTxt = data.afterTxt + '&#' + data.beforeTxt.charCodeAt(i) + ';';
                        break;
                    case 'url':
                        data.afterTxt = encodeURIComponent(data.beforeTxt);
                        break;
                    case 'base64':
                        var str=CryptoJS.enc.Utf8.parse(data.beforeTxt);
                        data.afterTxt = CryptoJS.enc.Base64.stringify(str);
                        break;
                    case 'html':
                        data.afterTxt = common.toUbb(data.beforeTxt);
                        break;
                    default:
                        break;
                }
                self.model.afterTxt = data.afterTxt;
            },
            decode(type) {
                let self = this;
                let data = {
                    beforeTxt: '',
                    afterTxt: this.model.afterTxt || ''
                };
                switch (type) {
                    case 'utf-8':
                        data.beforeTxt = unescape(data.afterTxt.replace(/&#x/g, '%u').replace(/;/g, ''));
                        break;
                    case 'ascii':
                        var character = data.afterTxt.split("\\u");
                        var native1 = character[0];
                        for (var i = 1; i < character.length; i++) {
                            var code = character[i];
                            native1 += String.fromCharCode(parseInt("0x" + code.substring(0, 4)));
                            if (code.length > 4) {
                                native1 += code.substring(4, code.length);
                            }
                        }
                        data.beforeTxt = native1;
                        break;
                    case 'unicode':
                        var code = data.afterTxt.match(/&#(\d+);/g);
                        if (code == null) {
                            break;
                        }
                        for (var i = 0; i < code.length; i++)
                            data.beforeTxt = data.beforeTxt + String.fromCharCode(code[i].replace(/[&#;]/g, ''));
                        break;
                    case 'url':
                        data.beforeTxt = decodeURIComponent(data.afterTxt);
                        break;
                    case 'base64':
                        var words  = CryptoJS.enc.Base64.parse(data.afterTxt);
                        data.beforeTxt=words.toString(CryptoJS.enc.Utf8);
                        break;
                    case 'html':
                        data.beforeTxt = common.toHtml(data.afterTxt);
                        break;
                    default:
                        break;
                }
                self.model.beforeTxt = data.beforeTxt;
            },
            onCopy() {
                layer.msg('复制成功')
            },
            onError(){
                console.log('复制失败')
            }
        }
    }

</script>