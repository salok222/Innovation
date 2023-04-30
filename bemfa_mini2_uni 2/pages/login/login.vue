<template>
    <view class="content">
        <view class="title">
            <p>登录</p>
        </view>
        <view>
            <p>账号</p>
            <input type="text" v-model="form.email" class="username">
            <p>密码</p>
            <input type="password" v-model="form.password" class="username">
        </view>
        <view>
            <button type="primary" class="login" @click="submit">登录</button>
        </view>
    </view>
</template>

<script>
    import {mapActions} from 'vuex'
    export default {
        data() {
            return {
                form:{
                    email: '',
                    password: '',
                }
            }
        },
        onLoad() {

        },
        methods: {
            ...mapActions([
                'handleLogin',
                'getUserInfo'
            ]),
            submit(){
                this.handleLogin(this.form).then(() => {
                    this.getUserInfo().then(() => {
                        uni.showToast({
                            icon: 'success',
                            position: 'bottom',
                            title: '登录成功'
                        })
                        // uni.navigateBack()   小程序用这个  把首页路由放第一个
                        uni.navigateTo({
                            url: '../index/index'
                        });
                    })
                })
            }
        }
    }
</script>

<style scoped>
    .title{
        font-weight: bold;
        margin: 10rpx 0 40rpx 0;
        font-size: 44rpx;
        color: white;
    }
    .username{
        height: 80rpx;
        margin:10rpx 0 40rpx 0;
        width: 600rpx;
        border-radius: 10rpx;
        border: #007aff 2rpx solid;
        background-color: rgba(200, 199, 204, 0.36);
    }
    .content {
        background-size: 100% 100%;
        padding: 70rpx;
        margin: 0 auto;
        text-align: center;
        height: 90.3vh;
    }
    .login{
        width: 400rpx;
        margin-top: 150rpx;
        color: white;
    }
</style>
