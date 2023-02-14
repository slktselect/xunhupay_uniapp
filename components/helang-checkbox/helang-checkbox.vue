<template>
	<view class="container" :class="column">
		<view v-for="(v,i) in list" :key="i" >
			<view 
			class="item"
			:class="{ 
				'active':(type=='radio' && index == i) || (type=='checkbox' && v.checked),
				'disabled':isDisabled
			}"
			:data-i="i"
			@tap="change"
			>{{v.text}}</view>
		</view>
	</view>
</template>

<script>
	export default {
		props:{
			// 标识名，用于多个组件时区分回调组件
			keyName:{
				type: Number | String,
				default: ''
			}
		},
		data() {
			return {
				list:[],
				index:-1,
				type:'checkbox',
				column:'',
				isDisabled:false,
				copyListData:'',
				copyIndexData:-1
			};
		},
		mounted() {
			
		},
		methods: {
			/* 切换 */
			change(e){
				if(this.disabled()){
					return;
				}
				let i = Number(e.currentTarget.dataset.i);
				/* 单选框 */
				if(this.type=='radio'){
					this.index = i;
					this.$nextTick(()=>{
						this.$emit("change",this.get(),this.$props.keyName);
					})
					return;
				}
				/* 复选框 */
				if(this.list[i].checked){
					this.$set(this.list[i],"checked",false);
				}else{
					if(this.maxSize){
						let pickerSize = 0;
						this.list.forEach((item,index)=>{
							if(item.checked){
								pickerSize++;
							}
						});
						// 当已选值数量 >= 允许的最大选择值时触发
						if(pickerSize >= this.maxSize){
							this.maxFn && this.maxFn();
							return;
						}
					}
					this.$set(this.list[i],"checked",true);
				}
				this.$nextTick(()=>{
					this.$emit("change",this.get(),this.$props.keyName);
				});
			},
			/* 设置值 */
			set(data) {
				let [type,index] = ['checkbox',-1];
				let column = ['','col_1','col_2','col_3'];
				if(data.type == 'radio'){
					type = 'radio';
					index = data.index >= 0 ? data.index : -1;
				}
				this.column = (data.column in column) ? column[data.column] : '';
				this.type = type;
				this.index = index;
				this.list = data.list;
				
				if(data.maxSize > 0 && data.maxFn){
					this.maxSize = data.maxSize;
					this.maxFn = data.maxFn;					
				}else{
					this.maxSize = undefined;
					this.maxFn = undefined;
				}
				
				// 存储数据
				this.copyListData = JSON.stringify(data.list);
				this.copyIndexData = data.index === undefined ? -1 : data.index;
			},
			/* 获取值 */
			get(){
				/* 单选框 */
				if(this.type=='radio'){
					if(this.index >= 0){
						return this.list[this.index];
					}else{
						return null;
					}
				}
				
				let arr=[];
				this.list.forEach((item,index)=>{
					if(item.checked == true){
						arr.push(item);
					}
				});
				return arr.length > 0 ? arr : null;
			},
			/* 全部选中 */
			checkAll(){
				if(this.disabled()){
					return;
				}
				if(this.type=='radio'){
					return null;
				}
				this.list.forEach((item,index)=>{
					this.$set(this.list[index],"checked",true);
				})
			},
			/* 取消全部选中 */
			cancelAll(){
				if(this.disabled()){
					return;
				}
				if(this.type=='radio'){
					this.index = -1;
					return null;
				}
				this.list.forEach((item,index)=>{
					this.$set(this.list[index],"checked",false);
				})
			},
			/* 反选全部 */
			invertAll(){
				if(this.disabled()){
					return;
				}
				if(this.type=='radio'){
					this.index = -1;
					return null;
				}
				this.list.forEach((item,index)=>{
					this.$set(this.list[index],"checked",item.checked ? false : true);
				})
			},
			/* 重置 */
			reset(){
				this.list = JSON.parse(this.copyListData);
				this.index = this.copyIndexData;
			},
			/* 禁用 */
			disabled(flag = undefined){
				if(flag === undefined){
					return this.isDisabled;
				}
				this.isDisabled = flag;
			}
		}
	}
</script>

<style lang="scss" scoped>
.container{
	display: flex;
    flex-direction: row;
    flex-wrap: wrap;
    justify-content: flex-start;
    align-items: flex-start;
    align-content: flex-start;
	margin-right: -20rpx;
	font-size: 28rpx;
	text-align: center;
	
	&>view{
		margin-bottom: 20rpx;
		padding-right: 20rpx;
		box-sizing: border-box;
	}
	
	&.col_1{
		&>view{
			width: 100%;
		}
	}
	&.col_2{
		&>view{
			width: 50%;
		}
	}
	&.col_3{
		&>view{
			width: 33.3333333%;
		}
	}
	
	.item{
		line-height: 68rpx;
		padding: 0 20rpx;
		box-sizing: border-box;
		border: #e5e5e5 solid 1px;
		background-color: #fff;
		color: #333;
		position: relative;
		overflow: hidden;
		white-space: nowrap;
		text-overflow: ellipsis;
		
		// 未选中状态下的禁用样式
		&.disabled{
			background-color: #f1f1f1;
			color: #d8d8d8;
		}
		
		&.active{
			background-color: #f5fff9;
			color: #42b983;
			border: #42b983 solid 1px;
			
			&::before{
				content: '';
				display:block;
				width: 20px;
				height: 20px;
				background-color: #42b983;
				position: absolute;
				right: -1px;
				bottom: -1px;
				z-index: 1;
				clip-path: polygon(100% 0, 0% 100%, 100% 100%);
			}
			&::after{
				content: '';
				display:block;
				width: 3px;
				height: 6px;
				border-right: #fff solid 2px;
				border-bottom: #fff solid 2px;
				transform:rotate(25deg);
				position: absolute;
				right: 3px;
				bottom: 3px;
				z-index: 2;
			}
			
			// 选中状态下的禁用样式
			&.disabled{
				background-color: #f1f1f1;
				color: #d8d8d8;
				border: #e5e5e5 solid 1px;
				
				&::before{
					background-color: #d9d9d9;
				}
			}
		}
	}
}
</style>
