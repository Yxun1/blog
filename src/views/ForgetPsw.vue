<template>
    <div class="bigBox">
        <div class="container" id="app1">
            <h2>forgetPsw</h2>
            <div class="form">
                <el-form :model="forgetPswForm" :rules="rules" ref="forgetPswForm" label-width="90px" label-height="10px"
                    color="red" :hide-required-asterisk="false" name="forgetPswForm">
                    <el-form-item label="账号" prop="name">
                        <el-input v-model.number="forgetPswForm.name" size="small"></el-input>
                    </el-form-item>
                    <el-form-item label="邮箱" prop="email">
                        <el-input v-model="forgetPswForm.email" autocomplete="off" size="small"></el-input>
                        <el-button size="small" @click="sendCode" :disabled="!forgetPswForm.show">
                            <span v-show="forgetPswForm.show">发送验证码</span>
                            <span v-show="!forgetPswForm.show">{{ forgetPswForm.count }}s后重新发送</span>
                        </el-button>
                    </el-form-item>
                    <el-form-item label="验证码" prop="code">
                        <el-input v-model="forgetPswForm.code" size="small"></el-input>
                    </el-form-item>
                    <el-form-item label="新的密码" prop="newPsw">
                        <el-input v-model="forgetPswForm.newPsw" size="small"></el-input>
                    </el-form-item>
                    <el-form-item class="btn">
                        <el-button size="small" @click="clear('forgetPswForm')">清除</el-button>
                        <el-button class="resetPsw" size="small" @click="resetPsw">重置密码</el-button>
                    </el-form-item>
                </el-form>
            </div>
        </div>
    </div>
</template>

<script>
// @ is an alias to /src
export default {
    name: 'app1',
    data() {
        return {
            forgetPswForm: {
                name: '',
                email: '',
                code: '',
                newPsw: '',
                show: true,
                timer: null, //设置计时器,
                count: '',
            },
            rules: {
                //校验数据
                name: [
                    { required: true, message: "请输入用户账号", trigger: "blur" },
                    { min: 5, max: 16, message: "长度不在5-16个字符", trigger: "blur" },
                ],
                email: [
                    { required: true, message: "请输入邮箱地址", trigger: "blur" },
                    { type: 'email', message: '请输入正确的邮箱地址', trigger: ['blur', 'change'] }
                ],
                code: [
                    { required: true, message: "请输入邮箱验证码", trigger: "blur" },
                ],
                newPsw: [
                    { required: true, message: "请输入密码", trigger: "change" },
                    { min: 8, max: 16, message: "长度在 8 到 16 个字符", trigger: "blur" },
                ],
            },
        };
    },
    methods: {
        clear(formName) {
            this.$refs[formName].resetFields();
        },
        sendCode() {
            this.dsq();
            this.$http({
                method: 'get',
                url: '/common/sendEmail',
                params: {
                    email: this.forgetPswForm.email
                }
            }).then((response) => {
                this.$message('验证码发送成功');
            }).catch((error) => {
                // 处理错误
            })
        },
        dsq() {
            this.forgetPswForm.show = false;
            // 设置倒计时时间
            this.forgetPswForm.count = 59;
            // 开始倒计时
            this.forgetPswForm.timer = setInterval(() => {
                this.forgetPswForm.count--;
                // 倒计时结束
                if (this.forgetPswForm.count === 0) {
                    // 清除计时器
                    clearInterval(this.forgetPswForm.timer);
                    // 设置按钮可点击
                    this.forgetPswForm.show = true;
                }
            }, 1000);
        },
        resetPsw() {
            this.$http({
                method: 'get',
                url: '/common/reset',
                params: {
                    name: this.forgetPswForm.name,
                    password: this.forgetPswForm.newPsw,
                    code: this.forgetPswForm.code
                }
            }).then((response) => {
                this.$message('重置成功，请登录！');
            }).catch((error) => {
                // 对 error 进行处理
            });
        }
        ,
    },

}

</script>
<style scoped>
.el-form-item {
    margin-bottom: 14px;
}

::v-deep .el-form-item label {
    color: rgba(255, 255, 255, 0.6);
    font-size: 16px;
}

.el-input {
    width: 170px;
    height: 10px;
}

.el-form-item__content {
    line-height: 40px;
}

.resetPsw {
    position: relative;
    left: 20px;
}



* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

.bigBox {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    height: 100vh;
    background: url("~@/assets/img/bg6.jpg") no-repeat;
    background-size: cover;

}

.container {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    width: 400px;
    height: 600px;
    border-radius: 3px;
    border-top: 1px solid rgba(255, 255, 255, 0.2);
    border-left: 1px solid rgba(255, 255, 255, 0.2);
    border-bottom: 1px solid rgba(255, 255, 255, 0.2);
    border-right: 1px solid rgba(255, 255, 255, 0.2);
    backdrop-filter: blur(20px);
}

.container>h2 {
    color: rgba(255, 255, 255, 0.9);
    position: relative;
    font-size: 40px;
    bottom: 30px;

}
</style>
