<template>
	<div id="app">
		<h1>Tarefas</h1>
		<TaskProgress :progress="progress"/>
		<NewTask @taskAdded="addTask"/>
		<TaskGrid 
			:tasks="tasks" 
			@taskDeleted="deleteTask" 
			@taskStateChange="toogleTaskState"/>
	</div>
</template>

<script>

import TaskGrid from './components/TaskGrid'
import NewTask from './components/NewTask'
import TaskProgress from './components/TaskProgress'

export default {
	components:{ TaskGrid, NewTask, TaskProgress },
	data(){
		return {
			tasks:[
				
			]
		}
	},
	computed:{
		progress(){
			const total = this.tasks.length;
			const done = this.tasks.filter(t => !t.pending).length;
			return Math.round(done / total * 100) || 0;
		}
	},
	watch:{
		tasks: {
			deep: true,
			handler(){
				localStorage.setItem('tasks', JSON.stringify(this.tasks))
			}
		}
	},
	methods:{
		addTask(task){
			const reallyNew = this.tasks.filter(p => p.name.toUpperCase() === task.name.toUpperCase()).length == 0;

			if(reallyNew){
				this.tasks.push({
					name: task.name,
					pending: task.pending || true
				})
			}

		},
		deleteTask(task){
			const index = this.tasks.indexOf(task);
			if(index != undefined)
				this.tasks.splice(index, 1);
		},
		toogleTaskState(task){
			task.pending = !task.pending;
		}
	},
	created(){
		const json = localStorage.getItem('tasks');
		const array = JSON.parse(json);
		
		if(Array.isArray(array)){
			this.tasks = array;
		}else{
			this.tasks = [];
		}
	}
}
</script>

<style>
	body {
		font-family: 'Lato', sans-serif;
		background: linear-gradient(to right, rgb(22, 34, 42), rgb(58, 96, 115));
		color: #FFF;
	}

	#app {
		display: flex;
		flex: 1;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		height: 100vh;
	}

	#app h1 {
		margin-bottom: 5px;
		font-weight: 300;
		font-size: 3rem;
	}
</style>
