<template>
    <div>
        <nav class="navbar navbar-light bg-light">
            <a href="/" class="navbar-brand">MEVN Stack</a>
        </nav>

        <div class="container">
            <div class="row pt-5">
                <div class="col-md-5">
                    <div class="card">
                        <div class="card-body">
                            <form @submit.prevent="sendTask() ">
                                <div class="form-group">
                                    <input v-model="task.title" type="text" placeholder="Insert a new task" class="form-control">
                                </div>
                                <div class="form-group">
                                    <textarea v-model="task.description" class="form-control" cols="30" rows="10" placeholder="Insert task description"></textarea>
                                </div>
                                <button v-if="!edit" class="btn btn-primary btn-block">Send task</button>
                                <button v-else class="btn btn-primary btn-block">Edit task</button>
                            </form>
                        </div>
                    </div>
                </div>
                <div class="col-md-7">
                    <table class="table table-bordered">
                        <thead>
                            <tr>
                                <th>Task Name</th>
                                <th>Task Description</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-for="task in tasks">
                                <td>{{task.title}}</td>
                                <td>{{task.description}}</td>
                                <td>
                                    <button @click="deleteTask(task._id)" class="btn btn-danger">Delete</button>
                                    <button @click="updateTask(task._id)" class="btn btn-secondary">Edit</button>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios';
    class Task {
        constructor(title, description) {
            this.title = title;
            this.description = description;
        }
    }
    export default {
        data(){
            return{
                task: new Task(),
                tasks: [],
                edit: false,
                taskToEdit: ''
            }
        },
        methods:{
            addTask(){
                let self = this;
                console.log(this.task);
                axios.post('/api/tasks', this.task)
                .then(function (response) {
                    console.log(response);
                    self.getTasks();
                })
                .catch(function (error) {
                    console.log(error);
                });
                this.task = new Task();
            },
            getTasks(){
                let self = this;
                axios.get('/api/tasks')
                .then(function (response) {
                    // handle success
                    self.tasks = response.data;
                })
                .catch(function (error) {
                    // handle error
                    console.log(error);
                });
            },
            deleteTask(id){
               let self = this;
                axios.delete('/api/tasks/'+id)
                .then(function (response) {
                    // handle success
                    self.getTasks();
                })
                .catch(function (error) {
                    // handle error
                    console.log(error);
                }); 
            },
            updateTask(id){
                let self = this;
                axios.get('/api/tasks/'+id)
                .then(function (response) {
                    // handle success
                    console.log(response.data);
                    self.task = new Task(response.data.title, response.data.description);
                    self.taskToEdit = response.data._id;
                    self.edit = true;
                })
                .catch(function (error) {
                    // handle error
                    console.log(error);
                });
            },
            sendTask(){
                let self = this;
                if(!this.edit)
                {
                    this.addTask();
                }
                else
                {
                    axios.put('/api/tasks/'+this.taskToEdit, this.task)
                    .then(function (response) {
                        console.log(response);
                        self.getTasks();
                        self.false = true;
                    })
                    .catch(function (error) {
                        console.log(error);
                    });
                }
            }
        },
        created(){
            this.getTasks();
        }
    }
</script>


<style>
</style>