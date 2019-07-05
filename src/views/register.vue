<template>
    <el-form :model="ruleForm2" :rules="rules2" ref="ruleForm2" label-position="left" label-width="0px" class="demo-ruleForm login-container">
        <h3 class="title">管理员注册</h3>
        <el-form-item prop="account">
            <el-input type="text" v-model="ruleForm2.account" auto-complete="off" placeholder="手机号">输入手机号</el-input>
        </el-form-item>
        <el-form-item prop="checkPass">
            <el-input type="password" v-model="ruleForm2.checkPass" auto-complete="off" placeholder="密码">输入密码</el-input>
        </el-form-item>

        <el-form-item style="width:100%;">
            <el-button type="primary" style="width:100%;" @click.native.prevent="handleSubmit2" :loading="logining">注册</el-button>
        </el-form-item>

    </el-form>

    <!--<div>-->
        <!--<p class="fl">-->
            <!--<input  name="phone" type="number" placeholder="手机号" v-model="phone"/>-->
            <!--<button type="button" :disabled="disabled" @click="sendcode" class="btns">{{btntxt}}</button>-->
        <!--</p>-->
        <!--<p class="fl" style="margin-left: 20px;">-->
            <!--<input type="text" placeholder="验证码"/>-->
        <!--</p>-->
    <!--</div>-->
    <!--<input type="button" value="查询"  class="btns search" @click="query"/>-->
</template>

<script>

    export default {
        // components: {Register, ElTabPane},
        data() {
            return {
                ss:{phone:'',
                    password:''},
                logining: false,
                ruleForm2: {
                    account: '',
                    checkPass: ''
                },
                rules2: {
                    account: [
                        { required: true, message: '请输入手机号', trigger: 'blur' },
                        //{ validator: validaePass }
                    ],
                    checkPass: [
                        { required: true, message: '请输入密码', trigger: 'blur' },
                        //{ validator: validaePass2 }
                    ]
                },
                checked: true
            };
        },
        methods: {

            async handleSubmit2() {

                // this.$refs.addForm.validate((valid) => {
                //     if(valid){
                //         this.$confirm('确认注册吗？', '提示', {}).then(async () => {
                //             this.addLoading = true;
                //             //NProgress.start();
                //             await this.$http.post("http://172.17.173.150:8080/signin/users", {
                //                 'shenfen': 2,
                //                 'phone': this.ruleForm2.account,
                //                 'password': this.ruleForm2.checkPass
                //             })
                //         }
                //     }
                // });

                this.$refs.ruleForm2.validate((valid) => {
                    if (valid) {
                        this.$confirm('确认注册吗？', '提示', {}).then(async () => {

                            await this.$http.post("http://172.17.174.232:8080/signin/users", {
                                'shenfen':2,
                                'phone':this.ruleForm2.account,
                                'password':this.ruleForm2.checkPass
                            })
                            console.log("经过了注册");
                            this.$router.push({path : '/login'});
                        });
                    }
                });
            }
        }
    }


    // export default {
    //     data: function () {
    //         return {
    //             disabled:false,
    //             time:0,
    //             btntxt:"获取验证码",
    //             formMess:{
    //                 email:this.email,
    //                 phone:this.phone
    //             }
    //         }
    //     },
    //     mounted: function () {
    //
    //     },
    //     methods:{
    //         //验证手机号码部分
    //         sendcode(){
    //             var reg=11 && /^((13|14|15|17|18)[0-9]{1}\d{8})$/;
    //             //var url="/nptOfficialWebsite/apply/sendSms?mobile="+this.ruleForm.phone;
    //             if(this.phone==''){
    //                 alert("请输入手机号码");
    //             }else if(!reg.test(this.phone)){
    //                 alert("手机格式不正确");
    //             }else{
    //                 this.time=60;
    //                 this.disabled=true;
    //                 this.timer();
    //                 /*axios.post(url).then(
    //                     res=>{
    //                     this.phonedata=res.data;
    //                 })*/
    //             }
    //         },
    //         timer() {
    //             if (this.time > 0) {
    //                 this.time--;
    //                 this.btntxt=this.time+"s后重新获取";
    //                 setTimeout(this.timer, 1000);
    //             } else{
    //                 this.time=0;
    //                 this.btntxt="获取验证码";
    //                 this.disabled=false;
    //             }
    //         },
    //         query(){
    //             var formMess=this.formMess
    //             Axios.post(api+"/order/select/reception", formMess)
    //                 .then(function (res) {
    //                     if(res.data.code==200){
    //                         console.log(res.data.data);
    //                         this.productResult=res.data.data;
    //                         this.productResult.length=3;
    //                     }else if(res.data.code==400){
    //                         alert(res.data.message)
    //                     }
    //
    //                 }.bind(this))
    //         },
    //         //邮箱验证
    //         sendEmail(){
    //             var regEmail=/^[A-Za-zd]+([-_.][A-Za-zd]+)*@([A-Za-zd]+[-.])+[A-Za-zd]{2,5}$/;
    //             if(this.email==''){
    //                 alert("请输入邮箱");
    //             }else if(!regEmail.test(this.email)){
    //                 alert("邮箱格式不正确");
    //             }
    //         }
    //     }
    // }

</script>

<style lang="scss" scoped>
    .login-container {
        /*box-shadow: 0 0px 8px 0 rgba(0, 0, 0, 0.06), 0 1px 0px 0 rgba(0, 0, 0, 0.02);*/
         -webkit-border-radius: 5px;
        border-radius: 5px;
         -moz-border-radius: 5px;
        background-clip: padding-box;
        margin: 180px auto;
        width: 350px;
        padding: 35px 35px 15px 35px;
        background: #fff;
        border: 1px solid #eaeaea;
        box-shadow: 0 0 25px #cac6c6;
        .title {
            margin: 0px auto 40px auto;
            text-align: center;
            color: #505458;
        }
        .remember {
            margin: 0px 0px 35px 0px;
        }
    }
</style>
