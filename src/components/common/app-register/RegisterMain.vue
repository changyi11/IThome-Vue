<template>
    <div>
        <form @submit.prevent = 'register' class="register">
            <div class='register-main'>
                <div class="register-text">使用软媒通行证，畅享软媒所有服务</div>
                <div class="register-product">
                    <img v-for = 'item in productli' :key = 'item.id' :src = 'item.src'>
                </div>
                <div class="register-input">
                    <div class="register-input_username">
                        <input v-model = 'phone' type="text" class="user-input" placeholder="手机号">
                    </div>
                    <div class="register-input_password">
                        <input v-model = 'code' type="text" class="user-password" placeholder="短信验证码" >
                    </div>
                    <a @click = 'sendCode' class="user-forget">{{ isResend ? '重发('+resendTime+'s)' : '获取验证码' }}</a>
                    <button type='submit' class="register-input_btn">
                            注册
                    </button>
                    <div class="register-input_a">
                        <router-link to="/login"> 
                            我已有软媒通行证，立即登录
                        </router-link>  
                    </div>
                </div>
                <div class="register-hr">
                    <img class='register-line' src='/images/line.png'>
                    一键登录
                    </div>
                <div class="register-others">
                    <a>
                        <img v-for = 'item in othersregister' :key = 'item.id'  :src= 'item.src'>
                    </a>
                </div>
            </div>
        </form>
        <div class='colse-btn'>
            <router-link to = '/home'>
                ×
            </router-link>
        </div>
    </div>
</template>


<script>
import { Toast } from 'mint-ui'
export default {
    data () {
        return {
            phone: '',
            code: '',
            isResend: false,
            resendTime: 60,
            productli: [
                { id:1, src: '/images/ithome.png'},
                { id:2, src: '/images/qiyu.png'},
                { id:3, src: '/images/lapin.png'}
            ],
            othersregister: [
                { id:1, src: '/images/iconqq.png'},
                { id:2, src: '/images/iconwechat.png'},
                { id:3, src: '/images/iconweibo.png'}
            ]
        }
    },
    methods: {
        async sendCode () {
            if( !this.isResend){
                let res = await this.$http({
                    url: '/mz/v4/api/code',
                    method: 'POST',
                    data: {
                        mobile: this.phone,
                        type: "2"
                    }
                }) 
                if ( res.status === 0 ) { // 验证码发送成功
                    this.authCode()
                }
            }

        },
        async register () {
            let res = await this.$http({
                url: '/mz/v4/api/login',
                method: 'POST',
                data: {
                    loginType: 1,
                    password: this.code,
                    username: this.phone
                }
            })
            //这里无法判断验证码是否正确了，因为后端接口多了一个图形验证码
            if( res.status ) {             // 等于0就是认证成功，那么存储他的手机号和验证码还有用户名
                Toast({
                    message: '注册成功',
                    position: 'middle',
                    className: 'toast',
                    duration: 1000
                })
                localStorage.setItem('userInfo',JSON.stringify({username: this.phone, password: this.code}))
                setTimeout( () => {
                    this.$router.push('login')
                },1000)
            }
        },
        authCode () {
            this.isResend = true
            this.timer = setInterval(() => {
                this.resendTime --
                if ( this.resendTime === 0 ) {
                    clearInterval(this.timer)
                    this.isResend = false
                    this.resendTime = 60
                }
            },1000)
        }
    }
}
</script>

<style lang="scss">
    .colse-btn{
        position: absolute;
        top: 0;
        right: .266667rem;
        a{
            font-size: 1.066667rem;
            color: #777;
        }
    }
    .register {
        font: 12px "Microsoft Yahei";
        width: 7.2rem;
        height: 13.333333rem;
        margin: 10% auto;
        border: none;
        .register-main {
            height: 100%;
            width: 100%;
            .register-text {   
                font-size: .346667rem;
                margin-top: .346667rem;
                color: #666;
                text-align: center;
            }
            .register-product {
                margin-top: .453333rem;
                text-align: center;
                img {
                    width: .853333rem;
                    height: .853333rem;
                    margin: .133333rem;
                }
            }
            .register-input {
                text-align: left;
                z-index: 10000;
                height: 8.266667rem;
                .register-input_username {
                    height: 1.733333rem;
                    border-bottom: solid 1px;
                    border-bottom-color: #ccc;
                    vertical-align: bottom;
                    .user-input {
                        margin: .933333rem .133333rem 0 0;
                        width: 7.2rem;
                        outline: none;
                        border: none;
                        font-size: .373333rem;
                        color: #888;
                    }
                }
                .register-input_password {
                    display: inline-block;
                    height: 1.733333rem;
                    width: 3.84rem;
                    border-bottom: solid 1px;
                    border-bottom-color: #ccc;
                    .user-password {
                        height: 19px;
                        margin: .933333rem .133333rem 0 0 ;
                        width: 100%;
                        outline: none;
                        border: none;
                        font-size: .373333rem;
                        color: #888;
                    }
                }
                .user-forget {
                    float: right;
                    display: inline-block;
                    width: 2.88rem;
                    height: 1.146667rem;
                    line-height: 1.146667rem;
                    margin-top: .453333rem;
                    text-align: center;
                    font-size: .32rem;
                    color: #687597;
                    border: solid 1px #687597;
                    border-radius: 22px;
                }
                .register-input_btn {
                    border: none;
                    width: 7.226667rem;
                    height: 1.2rem;
                    line-height: 1.2rem;
                    background-color: #416EAF;
                    border-radius: .586667rem;
                    font-size: .426667rem;
                    display: block;
                    color: #F4FFFF;
                    margin-top: .506667rem;
                    margin-bottom: .453333rem;
                    outline: none;
                    text-align: center;
                }
                .register-input_a {
                    color: #5b8bc3;
                    margin-left: 1.52rem;
                    text-decoration: underline;
                }
            }
            .register-hr {
                width: 7.2rem;
                color: #8A8A8A;
                margin-bottom: .266667rem;
                text-align: center;
                .register-line {
                    position: relative;
                    top: .373333rem;
                }
            }
            .register-others {
                text-align: center;
                img {
                    margin: .266667rem;
                    width: 1.066667rem;
                    height: 1.066667rem;
                }
            }
        }
    }
</style>


