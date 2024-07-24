<template>
	<view class="uni-container">
		<uni-forms ref="form" :value="formData" validateTrigger="bind">
		<!-- 	<uni-forms-item name="role_id" label="唯一ID" required>
				<uni-easyinput placeholder="角色唯一标识，不可修改，不允许重复" trim="both"
					disabled></uni-easyinput>
			</uni-forms-item> -->
			<uni-forms-item label="联系人" required name="name">
				<uni-easyinput v-model="formData.name" placeholder="请输入姓名" />
			</uni-forms-item>
			<uni-forms-item label="联系方式" required name="phone">
				<uni-easyinput v-model="formData.age" placeholder="请输入联系方式" />
			</uni-forms-item>
	<!-- 		<uni-forms-item label="状态" required name="status">
				<uni-easyinput v-model="formData.status" placeholder="请输入状态" />
			</uni-forms-item> -->
			<uni-forms-item name="role_name" label="需求" required>
				<uni-data-picker v-model="formData.requirement" :localdata="items" popup-title="请选择班级"
					@change="onchange" @nodeclick="onnodeclick"></uni-data-picker>
			</uni-forms-item>
			<!-- <uni-forms-item name="permission" label="权限" class="flex-center-x">
				<uni-data-checkbox :multiple="true" v-model="formData.user_id" collection="uni-id-permissions"
					:page-size="500" field="permission_name as text, permission_id as value"></uni-data-checkbox>
			</uni-forms-item> -->
			<uni-forms-item name="comment" label="备注">
				<uni-easyinput type="textarea" placeholder="备注" v-model="formData.introduction"
					trim="both"></uni-easyinput>
			</uni-forms-item>
			<view class="uni-button-group">
				<button type="primary" class="uni-button" style="width: 100px;"
					@click="submit(formData)">{{$t('common.button.submit')}}</button>
				<navigator open-type="navigateBack" style="margin-left: 15px;">
					<button class="uni-button" style="width: 100px;">{{$t('common.button.back')}}</button>
				</navigator>
			</view>
		</uni-forms>
	</view>
</template>

<script>
	import {
		validator
	} from '@/js_sdk/validator/uni-id-roles.js';

	const db = uniCloud.database();
	const dbCmd = db.command;
	const dbCollectionName = 'orders';

	// function getValidator(fields) {
	// 	let result = {}
	// 	for (let key in validator) {
	// 		if (fields.includes(key)) {
	// 			result[key] = validator[key]
	// 		}
	// 	}
	// 	return result
	// }

	export default {
		data() {
			let formData = {
				name: "",
				age: "",
				requirement: "",
				introduction: "",
			}
			return {
				items: [{
						text: "衣",
						value: "1-0",
						children: [{
								text: "买衣服",
								value: "买衣服"
							},
							{
								text: "补衣服",
								value: "补衣服"
							}
						]
					},
					{
						text: "食",
						value: "2-0",
						children: [{
								text: "买吃的",
								value: "买吃的"
							},
							{
								text: "做吃的",
								value: "做吃的"
							}
						]
					},
					{
						text: "洁",
						value: "3-0",
						children: [{
							text: "钟点工",
							value: "钟点工"
						}]
					},
					{
						text: "医",
						value: "4-0",
						children: [{
								text: "联系医生",
								value: "联系医生"
							},
							{
								text: "买药",
								value: "买药"
							}
						]
					},
					{
						text: "修",
						value: "5-0",
						children: [{
								text: "修电器",
								value: "修电器"
							},
							{
								text: "修其他",
								value: "修其他"
							}
						]
					}
				],
				formData,
				formDataId: {},
				formOptions: {},
				rules: {
					id:{
						rules: [{
							required: true,
							errorMessage: 'id不能为空'
						}]
					},
					user_id: {
						rules: [{
							required: true,
							errorMessage: '姓id不能为空'
						}]
					},
					name: {
						rules: [{
							required: true,
							errorMessage: '姓名不能为空'
						}]
					},
					age: {
						rules: [{
							required: true,
							errorMessage: '年龄不能为空'
						}, {
							format: 'number',
							errorMessage: '年龄只能输入数字'
						}]
					},
					status: {
						rules: [{
							required: true,
							errorMessage: '状态不能为空'
						}, {
							format: 'number',
							errorMessage: '状态只能输入数字'
						}]
					},
					requirement: {
						rules: [{
							required: true,
							errorMessage: '需求不能为空'
						}]
					},
					introduction: {
						rules: [{
							required: true,
							errorMessage: '需求详情不能为空'
						}]
					}
				}
			}
		},
		onLoad(e) {
			if (e.id) {
				// const id = e.id
				this.formDataId = e.id;
				// console.log(this.formDataId);
				this.getDetail(e.id);
			}
		},
		// onReady() {
		// 	this.$refs.form.setRules(this.rules)
		// },
		methods: {
			/**
			 * 触发表单提交
			 */
			submit(value) {
				// let formData2 = {
				// 	"name": this.formData.name,
				// 	"age": this.formData.age,
				// 	"requirement": this.formData.requirement,
				// 	// "permission": [],
				// 	"introduction":this.formData.introduction 
				// }
				// uni.showLoading({
				// 	mask: true
				// })
				this.$refs.form.validate().then((res) => {
					db.collection(dbCollectionName).doc(this.formDataId).update({
					name: value.name,
					age: value.age,
					requirement: value.requirement,
					introduction:value.introduction 	
					}).then((res) => {
						uni.showToast({
							title: '修改成功'
						})
					}).catch((err) => {
						uni.showModal({
							content: err.message || '请求服务失败',
							showCancel: false
						})
					}).finally(() => {
						uni.hideLoading()
					})
				}).catch(() => {
					uni.hideLoading()
				})
			},

			// submitForm(value) {
			// 	// 使用 clientDB 提交数据
			// 	db.collection(dbCollectionName).doc(this.formData._id).update(value).then((res) => {
			// 		uni.showToast({
			// 			title: '修改成功'
			// 		})
			// 		this.getOpenerEventChannel().emit('refreshData')
			// 		setTimeout(() => uni.navigateBack(), 500)
			// 	}).catch((err) => {
			// 		uni.showModal({
			// 			content: err.message || '请求服务失败',
			// 			showCancel: false
			// 		})
			// 	}).finally(() => {
			// 		uni.hideLoading()
			// 	})
			// },

			/**
			 * 获取表单数据
			 * @param {Object} id
			 */
			getDetail(id) {
				uni.showLoading({
					mask: true
				})
				db.collection(dbCollectionName).doc(this.formDataId).field(
					"requirement,introduction,name,age").get().then((res) => {
					const data = res.result.data[0]
					if (data) {
						this.formData = data
					}
				}).catch((err) => {
					uni.showModal({
						content: err.message || '请求服务失败',
						showCancel: false
					})
				}).finally(() => {
					uni.hideLoading()
				})
			}
		}
	}
</script>
<style>
	::v-deep .uni-forms-item__label {
		width: 90px !important;
	}
</style>