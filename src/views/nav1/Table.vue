<template>
	<section>
		<!--工具条-->
		<el-col :span="24" class="toolbar" style="padding-bottom: 0px;">
			<el-form :inline="true" :model="filters">
				<el-form-item>
					<el-input v-model="filters.name" placeholder="姓名"></el-input>
				</el-form-item>
				<el-form-item>
				<el-button type="primary" v-on:click="findUser">查询</el-button>
			</el-form-item>
				<el-form-item>
					<el-button type="primary" @click="handleAdd">新增</el-button>
				</el-form-item>
			</el-form>
		</el-col>

		<!--列表-->
		<el-table :data="users" highlight-current-row v-loading="listLoading" @selection-change="selsChange" style="width: 100%;">
			<!--<el-table-column type="selection" width="55">-->
			<!--</el-table-column>-->
			<el-table-column type="index" label="序号" width="80">
			</el-table-column>
			<el-table-column prop="xuehao" label="学\工号" width="120" sortable>
			</el-table-column>
			<el-table-column prop="name" label="姓名" width="130" sortable>
			</el-table-column>
			<el-table-column prop="phone" label="电话" width="130" sortable>
			</el-table-column>

			<el-table-column prop="mail" label="邮箱" min-width="120" sortable>
			</el-table-column>
				<el-table-column prop="shenfen" label="身份" width="100" :formatter="formatShenfen" sortable>
				</el-table-column>
				<el-table-column prop="sex" label="性别" min-width="80" :formatter="formatSex"sortable>
				</el-table-column>
			<el-table-column label="操作" width="150">
				<template scope="scope">
					<el-button size="small" @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
					<el-button type="danger" size="small" @click="handleDel(scope.$index, scope.row)">删除</el-button>
				</template>
			</el-table-column>
		</el-table>

		<!--工具条-->
		<el-col :span="24" class="toolbar">
			<!--<el-button type="danger" @click="batchRemove" :disabled="this.sels.length===0">批量删除</el-button>-->
			<!--<el-pagination layout="prev, pager, next" @current-change="handleCurrentChange" :total="total" style="float:right;">-->
				<el-pagination layout="prev, pager, next" @current-change="handleCurrentChange" :page-size="pagesize" :total="total" style="float:right;">
			</el-pagination>
		</el-col>

		<!--编辑界面-->
		<el-dialog title="编辑" v-model="editFormVisible" :close-on-click-modal="false">
			<el-form :model="editForm" label-width="80px" :rules="editFormRules" ref="editForm">
				<el-form-item label="学\工号" prop="number">
					<el-input v-model="editForm.number" auto-complete="off"></el-input>
				</el-form-item>
				<el-form-item label="姓名" prop="name">
					<el-input v-model="editForm.name" auto-complete="off"></el-input>
				</el-form-item>
				<el-form-item label="电话" prop="phone">
					<el-input type="textarea" v-model="editForm.phone"></el-input>
				</el-form-item>
				<el-form-item label="邮箱" prop="mail">
					<el-input type="textarea" v-model="editForm.mail"></el-input>
				</el-form-item>
				<el-form-item label="密码" prop="password">
					<el-input type="textarea" v-model="editForm.password"></el-input>
				</el-form-item>
				<el-form-item label="性别">
					<el-radio-group v-model="editForm.sex">
						<el-radio class="radio" :label="1">男</el-radio>
						<el-radio class="radio" :label="0">女</el-radio>
					</el-radio-group>
				</el-form-item>
			</el-form>
			<div slot="footer" class="dialog-footer">
				<el-button @click.native="editFormVisible = false">取消</el-button>
				<el-button type="primary" @click.native="editSubmit" :loading="editLoading">提交</el-button>
			</div>
		</el-dialog>

		<!--新增界面-->
		<el-dialog title="新增" v-model="addFormVisible" :close-on-click-modal="false">
			<el-form :model="addForm" label-width="80px" :rules="addFormRules" ref="addForm">
				<el-form-item label="学\工号" prop="number">
					<el-input v-model="addForm.number" auto-complete="off"></el-input>
				</el-form-item>
				<el-form-item label="姓名" prop="name">
					<el-input v-model="addForm.name" auto-complete="off"></el-input>
				</el-form-item>
				<el-form-item label="电话" prop="phone">
					<el-input v-model="addForm.phone" auto-complete="off"></el-input>
				</el-form-item>
				<el-form-item label="密码" prop="password">
					<el-input v-model="addForm.password" auto-complete="off"></el-input>
				</el-form-item>
				<el-form-item label="身份" >
					<el-radio-group v-model="addForm.shenfen">
						<el-radio class="radio" :label="0">教师</el-radio>
						<el-radio class="radio" :label="1">学生</el-radio>
						<el-radio class="radio" :label="2">管理员</el-radio>
					</el-radio-group>
				</el-form-item>
				<el-form-item label="性别">
					<el-radio-group v-model="addForm.sex">
						<el-radio class="radio" :label="1">男</el-radio>
						<el-radio class="radio" :label="0">女</el-radio>
					</el-radio-group>
				</el-form-item>
				<el-form-item label="邮箱" prop="mail">
					<el-input type="textarea" v-model="addForm.mail"></el-input>
				</el-form-item>
			</el-form>
			<div slot="footer" class="dialog-footer">
				<el-button @click.native="addFormVisible = false">取消</el-button>
				<el-button type="primary" @click.native="addSubmit" :loading="addLoading">提交</el-button>
			</div>
		</el-dialog>
	</section>
</template>

<script>
	import util from '../../common/js/util'
	
	//import NProgress from 'nprogress'
	//import { getUserListPage, removeUser, batchRemoveUser, editUser, addUser } from '../../api/api';

	export default {
		data() {
			return {
				filters: {
					name: ''
				},
				 users: [],
				//users: User1[],

				total: 0,
				pagesize:20,
				page: 1,
				listLoading: false,
				sels: [],//列表选中列

				editFormVisible: false,//编辑界面是否显示
				editLoading: false,
				editFormRules: {
					number: [
						{ required: true, message: '请输入学号', trigger: 'blur' }
					],
					name: [
						{ required: true, message: '请输入姓名', trigger: 'blur' }
					],
					phone: [
						{ required: true, message: '请输入电话', trigger: 'blur' }
					],
					password: [
						{ required: true, message: '请输入密码', trigger: 'blur' }
					],
					mail: [
						{ required: true, message: '请输入邮箱', trigger: 'blur' }
					]
				},
				//编辑界面数据
				editForm: {
					number: '',
					name: '',
					phone : '',
					mail : '',
					password : '',
					sex : ''
				},

				addFormVisible: false,//新增界面是否显示
				addLoading: false,
				addFormRules: {
					number: [
						{ required: true, message: '请输入学号', trigger: 'blur' }
					],
					name: [
						{ required: true, message: '请输入姓名', trigger: 'blur' }
					],
					phone: [
						{ required: true, message: '请输入电话', trigger: 'blur' }
					],
					password: [
						{ required: true, message: '请输入密码', trigger: 'blur' }
					],
					mail: [
						{ required: true, message: '请输入邮箱', trigger: 'blur' }
					]
				},
				//新增界面数据
				addForm: {
					number: '',
					name: '',
					phone : '',
					mail : '',
					password : '',
					shenfen : 0 ,
					sex : ''
				}

			}
		},
		methods: {
			//性别显示转换
			formatShenfen: function (row, column) {
					return row.shenfen == 0 ? '教师' : row.shenfen == 1 ? '学生	' : '管理员';
				},
			formatSex: function (row, column) {
				return row.sex == "0" ? '女' : '男	';
			},
			//显示用户列表
			async userList() {
				this.listLoading = true;
				await this.$http
						.get("http://kangkangin.asia:8080/signin/users?pageNum="+this.page+"&pageSize="+this.pagesize+"&shenfen=2")
						.then(res => {
							// console.log("用户列表"+res['body'].data);//你需要在前端定义一个数组接收,此时，打印结果是object，想看结果的话JSON.stringify(res['body'].data)，所以要定义一个数组，例如前端user[] 接收，你百度解决;
							this.listLoading = false;
							this.total=res.data.data.userList.total;
							this.users = res.data.data.userList.list;
							 console.log("this.page======"+this.page);
							 console.log(this.users);
						});
			},
			//查询用户
			async findUser() {
				this.listLoading = true;
				let para = {

					name: this.filters.name
				};
				if (para.name == ''){
					console.log("进入空查询分支");
					this.userList();
					}
					else {
						await this.$http.get("http://kangkangin.asia:8080/signin/users?name=" + para.name + "&shenfen=2")
								.then(res => {
									this.listLoading = false;
									this.users = res.data.data.userList.list;
								})
					}
			},
			async testAddUser() {
				await this.$http
						.post("http://kangkangin.asia:8080/signin/addUser", {
							'xuehao':this.addForm.number,
							'name':this.addForm.name,
							'phone':this.addForm.phone,
							'mail':this.addForm.mail,
							'password':this.addForm.password,
							'shenfen':this.addForm.shenfen,
							'sex':this.addForm.sex,
						})
						.then(res => {
							console.log("添加信息："+res['body'].resultMessage);//因为存在重复添加所以会返回信息，学号和身份同时重复视为添加失败;
						});
			},


			handleCurrentChange(val) {
				this.page = val;
				this.userList();
			},
			//获取用户列表
			// getUsers() {
			// 	let para = {
			// 		page: this.page,
			// 		name: this.filters.name
			// 	};
			// 	this.listLoading = true;
			// 	//NProgress.start();
			// 	getUserListPage(para).then((res) => {
			// 		this.total = res.data.total;
			// 		this.users = res.data.users;
			// 		this.listLoading = false;
			// 		//NProgress.done();
			// 	});
			// },
			//删除
			handleDel: function (index, row) {
				this.$confirm('确认删除该记录吗?', '提示', {
					type: 'warning'
				}).then(async () => {
					this.listLoading = true;
					//NProgress.start();
					let para = {xuehao: row.xuehao};
					console.log("要删除的学号是++++++++" + para.xuehao);
					await this.$http.delete("http://kangkangin.asia:8080/signin/users?xuehao=" + para.xuehao + "&shenfen=2")
							.then(res=>{
								this.userList();
							})
				}).catch(() => {

				});
			},
			//显示编辑界面
			handleEdit: function (index, row) {
				this.editFormVisible = true;
				this.editForm = Object.assign({}, row);
			},
			//显示新增界面
			handleAdd: function () {
				this.addFormVisible = true;
				this.addForm = {
					number: '',
					name: '',
					phone : '',
					mail : '',
					shenfen : 0 ,
					sex : ''
				};
			},
			//编辑
			editSubmit: function (row) {
				this.$refs.editForm.validate((valid) => {
					if (valid) {
						this.$confirm('确认提交吗？', '提示', {}).then(() => {
							this.editLoading = true;
							//NProgress.start();
							let para = Object.assign({}, this.editForm);
							console.log("row==="+para.xuehao);

							this.$http.put("http://kangkangin.asia:8080/signin/users?xuehao="+para.xuehao+"&shenfen=2", {
								'xuehao':this.editForm.number,
								'name':this.editForm.name,
								'phone':this.editForm.phone,
								'mail':this.editForm.mail,
								'password':this.editForm.password,
								'shenfen':2,
								'sex':this.editForm.sex,
							}).then((res) => {
								this.editLoading = false;
								//NProgress.done();
								this.$message({
									message: '提交成功',
									type: 'success'
								});
								this.$refs['editForm'].resetFields();
								this.editFormVisible = false;
								this.userList();
							});
						});
					}
				});
			},
			//新增
			 addSubmit: function () {
				this.$refs.addForm.validate((valid) => {
					if (valid) {
						this.$confirm('确认提交吗？', '提示', {}).then(async () => {
							this.addLoading = true;
							//NProgress.start();
							await this.$http.post("http://kangkangin.asia:8080/signin/users", {
								'xuehao': this.addForm.number,
								'name': this.addForm.name,
								'phone': this.addForm.phone,
								'mail': this.addForm.mail,
								'password': this.addForm.password,
								'shenfen': this.addForm.shenfen,
								'sex': this.addForm.sex,
							})
									.then(res => {
										if (res['body'].resultCode == 0) {
											this.$message({
												message: '请勿重复添加',
												type: 'error'
											});
										} else if (res['body'].resultCode == 2) {
											this.$message({
												message: '该手机号已经注册',
												type: 'error'
											});
										} else {
											this.$message({
												message: '提交成功',
												type: 'success'
											});
										}
										console.log("添加信息：" + res['body'].resultCode);
										// +res['body'].data.data.resultMessage);//因为存在重复添加所以会返回信息，学号和身份同时重复视为添加失败;
									});
							this.addLoading = false;
							this.$refs['addForm'].resetFields();
							this.addFormVisible = false;
							this.userList();
						});
					}
				});
			},
			selsChange: function (sels) {
				this.sels = sels;
			},
			//批量删除
			// batchRemove: function () {
			// 	var ids = this.sels.map(item => item.id).toString();
			// 	this.$confirm('确认删除选中记录吗？', '提示', {
			// 		type: 'warning'
			// 	}).then(() => {
			// 		this.listLoading = true;
			// 		//NProgress.start();
			// 		let para = { ids: ids };
			// 		batchRemoveUser(para).then((res) => {
			// 			this.listLoading = false;
			// 			//NProgress.done();
			// 			this.$message({
			// 				message: '删除成功',
			// 				type: 'success'
			// 			});
			// 			this.getUsers();
			// 		});
			// 	}).catch(() => {
			//
			// 	});
			// }
		},

		mounted() {
			this.userList();
		}
	}

</script>

<style scoped>

</style>
