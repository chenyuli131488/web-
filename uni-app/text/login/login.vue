<!-- 确认验证码 -->
<template>
    <view class="register">
        <view class="been-sent">验证码已发送至手机</view>
        <view class="phone-number">{{loginForm.phone}}</view>
        <view class="code">请输入验证码</view>
         
        <view class="dialog-bd">
            <view class="code-view">
                <view v-for="(code,index) of codeAry" :v-key="index" class="code-item">{{code.val}}</view>
            </view>
        </view>
        <!-- 验证码判断 -->
        <view class="time" v-if="ashow">
            <view class="date">{{num}}</view>
            <view class="date-content">后 可重发验证码</view>
        </view>
        <view v-else>
            <view class="again-content">还收不到验证码？请先确认手机是否安装了短信拦截软件或是欠费停机。均若不是，请尝试以下方式</view>
            <view class="again-send" @click="again">重发验证码</view>
        </view>
        <view class="keyboard">
            <view class="keyboard-line">
                <view data-val="1" @click="bindKeyEvent" class="button-item">1</view>
                <view data-val="2" @click="bindKeyEvent" class="button-item">2</view>
                <view data-val="3" @click="bindKeyEvent" class="button-item">3</view>
            </view>
            <view class="keyboard-line">
                <view data-val="4" @click="bindKeyEvent" class="button-item">4</view>
                <view data-val="5" @click="bindKeyEvent" class="button-item">5</view>
                <view data-val="6" @click="bindKeyEvent" class="button-item">6</view>
            </view>
            <view class="keyboard-line">
                <view data-val="7" @click="bindKeyEvent" class="button-item">7</view>
                <view data-val="8" @click="bindKeyEvent" class="button-item">8</view>
                <view data-val="9" @click="bindKeyEvent" class="button-item">9</view>
            </view>
            <view class="keyboard-line">
                <view data-val="clear" @click="bindKeyEvent" class="button-item">清空</view>
                <view data-val="0" @click="bindKeyEvent" class="button-item">0</view>
                <view data-val="delete" @click="bindKeyEvent" class="button-item">x</view>
            </view>
        </view>
    </view>
</template>
 
<script>
    import middle from "../../middleware/login.js"
    export default {
         
        data() {
            return {
                // 验证码四位数组
                codeAry: [{
                    "val": ""
                }, {
                    "val": ""
                }, {
                    "val": ""
                }, {
                    "val": ""
                }],
                // 个数
                currItem: 0,
                // 验证码
                callResult: {
                    type:0,
                    code:null
                },
                // 倒数重发显示切换
                ashow:true,
                // 验证码长度
                len: '4',
                // 发送参数
                loginForm:{
                    isLogin: true,
                    codeType:2
                },
                // 获取的类型  判断为注册用户还是忘记密码
                type:0,
                // 倒计时
                num:60,
                // 发送参数 判断用户是否存在
                numlist:{
                    number:'',
                },
 
            }
        },
        onLoad(options) {
            //接收上个页面的type
            this.type = options.type
            if(this.type == 1){
                this.loginForm.codeType = 3
            }
            // 页面加载获取上个页面穿的手机号码
            this.loginForm.phone = options.tel
            // 清除定时器
            clearInterval(this.timer);
            // 进入页面后就开始60秒倒数
            this.timer = setInterval(() => {
                this.num--;
                if(this.num == 0) {
                    this.ashow =false
                    clearInterval(this.timer)
                }
            },1000)
        },
 
        methods: {
            /**
            * @author 宫丽颖
            * @date 2019-06-19
            * @information 键盘点击事件
            */
            bindKeyEvent: function(e) {
                var _this = this;
                var val = e.currentTarget.dataset.val;
                // 键盘各种事件
                switch (val) {
                    case "clear":
                        _this.clearCode();
                        break;
                    case "delete":
                        _this.deleteCode();
                        break;
                    default:
                        _this.inputVal(val);
                        break;
                }
            },
            /*
            * @author 宫丽颖
            * @date 2019-06-19
            * @information 拼接验证码
            */
            getCodeValue:function(){
                var codeStr = "";
                this.codeAry.forEach(function(code){
                    codeStr+=code.val;
                })
                return codeStr;
            },
            /*
            * @author 宫丽颖
            * @date 2019-06-19
            * @information 清空内容
            */
            init: function() {
                var codeAry = [];
                for (var i = 0; i < this.len; i++) {
                    codeAry.push({
                        val: ""
                    })
                }
                this.codeAry = codeAry;
                this.currItem = 0;
            },
            /*
            * @author 宫丽颖
            * @date 2019-06-19
            * @information 清空内容事件
            */
            clearCode: function() {
                this.init();
            },
             
            /*
            * @author 宫丽颖
            * @date 2019-06-19
            * @information 回退x 事件
            */
            deleteCode: function() {
                if (this.currItem > 0) {
                    this.codeAry[this.currItem - 1].val = "";
                    this.currItem--;
                }
            },
            /**
             * @author 宫丽颖
             * @date 2019-06-19
             * @information 输入内容
             */
            inputVal: function(val) {
                if (this.currItem < this.len) {
                    this.codeAry[this.currItem].val = val;
                    this.currItem++;
                }
                if (this.currItem == this.len) {
                    this.execuCall(1);
                }
            },
             
            /*
            * @author 宫丽颖
            * @date 2019-06-19
            * @information 输入完成后验证验证码
            */
            execuCall:function(type){
                let _this = this
                this.callResult.type = type;
                if(type == 1){
                    this.callResult.code = this.getCodeValue();
                    this.loginForm.code = this.callResult.code
                    middle.codeLogin(this.loginForm).then(res => {
                        if(res.statusCode == 1000){
                            uni.setStorage({
                                key: "phone",
                                data: this.loginForm.phone
                            })
                            uni.showToast({
                                title: '验证通过：'+ this.loginForm.code,
                                duration: 2000
                            });
                            setTimeout(() => {
                                uni.navigateTo({
                                    url:`/pages/login/SetUpPassword?tel=${this.loginForm.phone}&type=${this.type}`
                                })
                            },500)
                        }
                        clearInterval(this.timer)
                    })
                    .catch(error =>{
                        uni.showModal({
                            title: '提示',
                            content: '验证码错误，请重新输入',
                            showCancel:false,
                            confirmColor:'#00c16f',
                            success: function (res) {
                                _this.init()
                            }
                        });
                    })
 
                }
            },
            /*
            * @author 宫丽颖
            * @date 2019-06-19
            * @information 重新发送验证码
            */
            again:function () {
                this.ashow = true
                this.num = 60;
                this.timer = setInterval(() => {
                    this.num--;
                    if(this.num == 0) {
                        this.ashow =false
                        clearInterval(this.timer)
                    }
                },1000)
                // 将手机号赋值给number
                this.numlist.number = this.loginForm.phone
                // 访问验证码接口
                middle.getCode(this.numlist)
                 
            },
        }
    }
</script>
 
<style>
</style>