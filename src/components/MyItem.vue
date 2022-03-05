<template>
	<transition
		apear
		name="animate__animated animate__bounce"
		enter-active-class="animate__fadeInDown"
		leave-active-class="animate__fadeOutDown"
	>
		<li>
			<label>
				<input type="checkbox" :checked="todo.done" @change="handleCheck(todo.id)"/>
				<!-- 如下代码也能实现功能，但是不太推荐，因为有点违反原则，因为修改了props -->
				<!-- <input type="checkbox" v-model="todo.done"/> -->
				<span v-show="!todo.isEdit">{{todo.title}}</span>
				<input v-show="todo.isEdit" type="text"
					ref="inputTitle"
					:value="todo.title"
					@blur="handleBlur(todo, $event)">
			</label>
			<button class="btn btn-danger" @click="handleDelete(todo.id)">删除</button>
			<button class="btn btn-edit" @click="handleEdit(todo)">编辑</button>
		</li>
	</transition>
</template>

<script>

	import "animate.css"

	export default {
		name:'MyItem',
		//声明接收todo
		props:['todo'],
		methods: {
			//勾选or取消勾选
			handleCheck(id){
				//通知App组件将对应的todo对象的done值取反
				// this.checkTodo(id)
				this.$bus.$emit('checkTodo',id)
			},
			//删除
			handleDelete(id){
				if(confirm('确定删除吗？')){
					//通知App组件将对应的todo对象删除
					// this.deleteTodo(id)
					this.$bus.$emit('deleteTodo',id)
				}
			},
			//编辑
			handleEdit(todo){
				if(todo.hasOwnProperty("isEdit")){
					todo.isEdit = true;
				}else{
					this.$set(todo,'isEdit',true);
				}

				//模板重新解析并不会发生在这段代码之前，而是在整个handleEdit执行完后生效。
				//因此此时input框并没有渲染出现，也就无从获取焦点
				//this.$refs.inputTitle.focus();

				/* nextTick作用：在下一次DOM更新结束后执行其指定的回调 
					什么时候使用：当改变数据后，要基于更新后的新DOM进行某些操作时，要在nextTick所指定的回调函数中执行
				*/
				//TODO:待复习:$nextTick
				this.$nextTick(function(){
					this.$refs.inputTitle.focus();
				})
			},
			//失去焦点回调
			handleBlur(todo,e){
				if(!e.target.value.trim()) return alert("输入无效");
				todo.isEdit = false;
				this.$bus.$emit("updateTodo", todo.id, e.target.value);
			}
		},
	}
</script>

<style scoped>
	/*item*/
	li {
		list-style: none;
		height: 36px;
		line-height: 36px;
		padding: 0 5px;
		border-bottom: 1px solid #ddd;
	}

	li label {
		float: left;
		cursor: pointer;
	}

	li label li input {
		vertical-align: middle;
		margin-right: 6px;
		position: relative;
		top: -1px;
	}

	li button {
		float: right;
		display: none;
		margin-top: 3px;
	}

	li:before {
		content: initial;
	}

	li:last-child {
		border-bottom: none;
	}

	li:hover{
		background-color: #ddd;
	}
	
	li:hover button{
		display: block;
	}
</style>