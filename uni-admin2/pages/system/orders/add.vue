<template>
  <view class="uni-container">
    <uni-forms ref="form" :value="formData" validateTrigger="bind">
     <uni-forms-item label="联系人" required name="name">
     	<uni-easyinput v-model="formData.name" placeholder="请输入姓名"/>
     </uni-forms-item>
     <uni-forms-item label="联系方式" required name="age">
     	<uni-easyinput v-model="formData.age" placeholder="请输入联系方式" />
     </uni-forms-item>
      <uni-forms-item name="role_name" label="需求" required>
       <uni-data-picker v-model="formData.requirement" :localdata="items" popup-title="请选择班级"
       	@change="onchange" @nodeclick="onnodeclick"></uni-data-picker>
      </uni-forms-item>
      <uni-forms-item name="permission" label="权限" class="flex-center-x">
        <uni-data-checkbox :multiple="true" v-model="formData.user_id" collection="uni-id-permissions" :page-size="500" field="permission_name as text, permission_id as value"></uni-data-checkbox>
      </uni-forms-item>
      <uni-forms-item name="comment" label="备注">
        <uni-easyinput type="textarea" placeholder="备注" v-model="formData.introduction" trim="both"></uni-easyinput>
      </uni-forms-item>
      <view class="uni-button-group">
        <button type="primary" class="uni-button" style="width: 100px;" @click="submit">{{$t('common.button.submit')}}</button>
        <navigator open-type="navigateBack" style="margin-left: 15px;">
            <button class="uni-button" style="width: 100px;">{{$t('common.button.back')}}</button>
        </navigator>
      </view>
    </uni-forms>
  </view>
</template>

<script>
  import { validator } from '@/js_sdk/validator/uni-id-roles.js';

  const db = uniCloud.database();
  const dbCmd = db.command;
  const dbCollectionName = 'orders';

  function getValidator(fields) {
    let result = {}
    for (let key in validator) {
      if (fields.includes(key)) {
        result[key] = validator[key]
      }
    }
    return result
  }

  export default {
    data() {
      let formData = {
		  "status": 1,
		  "name": "",
		 "age":"",
        // "_id": "",
        "requirement": "",
        // "user_id": "",
        "introduction": "",
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
        formOptions: {},
        rules: {
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
    // onReady() {
    //   this.$refs.form.setRules(this.rules)
    // },
    methods: {
      /**
       * 触发表单提交
       */
      submit() {
        uni.showLoading({
          mask: true
        })
        this.$refs.form.validate().then((res) => {
			console.log("下面是res===");
			console.log(res);
         // 使用 clientDB 提交数据
         db.collection(dbCollectionName).add(this.formData).then((res) => {
           uni.showToast({
             title: '新增成功'
           })
           this.getOpenerEventChannel().emit('refreshData')
           setTimeout(() => uni.navigateBack(), 500)
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
      //   // 使用 clientDB 提交数据
      //   db.collection(dbCollectionName).add(value).then((res) => {
      //     uni.showToast({
      //       title: '新增成功'
      //     })
      //     this.getOpenerEventChannel().emit('refreshData')
      //     setTimeout(() => uni.navigateBack(), 500)
      //   }).catch((err) => {
      //     uni.showModal({
      //       content: err.message || '请求服务失败',
      //       showCancel: false
      //     })
      //   }).finally(() => {
      //     uni.hideLoading()
      //   })
      // }
    }
  }
</script>
<style>
	::v-deep .uni-forms-item__label {
		width: 90px !important;
	}
</style>
