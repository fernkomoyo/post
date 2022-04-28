<template>

    <div>
        <div v-if="!loading">
            <img class="rounded mx-auto d-block" src="image" alt="loader">
        </div>
        <!-- 
        <button type="button" data-bs-toggle="modal" data-bs-target="#createModal" class="btn btn-primary btn-lg w-100">Add new post</button>
        <table class="table" v-if="tasks">
            <thead>
                <tr>
                    <th>Id</th>
                    <th>Name</th>
                    <th>Body</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="(task, index) in tasks">
                    <td>{{index +1}}</td>
                    <td>{{task.name}}</td>
                    <td>{{task.body }}</td>
                    <td><img :src="task.file_path" width="128" height="128"></td>
                    <td><button @click="loadUpdateModal(index)" type="button" class="btn btn-info" data-bs-toggle="modal" data-bs-target="#updateModal">Edit</button></td>
                    <td><button @click="deleteTask(index)" class="btn btn-danger">Delete</button></td>
                </tr>
            </tbody>
        </table>
        -->

        <!-- presentacion de bootstrap cards!!! -->
        <div class="card-deck">
            <div class="row">
                <!-- Boton para crear con bootstrap card -->
                <div class="card bg-dark text-white" style="width: 18rem;">
                <div class="card-body">
                    <h5 class="card-title">Create new</h5>
                    <a href="#" class="btn stretched-link" type="button" data-bs-toggle="modal" data-bs-target="#createModal"></a>
                </div>
                </div>

            <div class="col-md-4 col-6 my-1 d-flex align-items-stretch" v-for="(task, index) in tasks">
                <div class="card bg-dark text-white" style="width: 18rem;">
                <img class="card-img-top" :src="task.file_path" alt="Card image cap">
                <div class="card-body">
                    <h5 class="card-title">{{task.name}}</h5>
                    <p class="card-text">{{task.body }}</p>
                    <hr>
                    <button @click="loadUpdateModal(index)" type="button" class="btn btn-outline-warning w-20" data-bs-toggle="modal" data-bs-target="#updateModal">Edit</button>
                    <button @click="deleteTask(index)" class="btn btn-outline-danger w-20">Delete</button>
                </div>
                </div>
            </div>
            </div>
        </div>       


        <!-- Create Modal -->
            <div class="modal fade" id="createModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
            <div class="modal-dialog">
                <div class="modal-content bg-dark text-white">
                <div class="modal-header">
                    <h5 class="modal-title text-justify" id="exampleModalLabel">Create Post</h5>
                    <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                </div>
                <div class="modal-body ">

                    <div class="alet alert-danger" v-if="errors.length > 0">
                        <ul>
                            <li v-for="error in errors">{{error}}</li>
                        </ul>
                    </div>

                    <div class="form-group border-white">
                        <label for="name">Post Title</label>
                        <input v-model="task.name" type="text" id="name" class="form-control border-white">
                    </div>
                    <div class="form-group">
                        <label for="description">Description</label>
                        <input v-model="task.body" type="text" id="description" class="form-control">
                    </div>
                    <label for="description">Add Image</label>
                    <form action="/file-upload" class="form-control dropzone" id="dropzone">
                        <div class="fallback">
                            <input name="file" type="file" multiple />
                        </div>
                    </form>
                    
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-outline-danger" data-bs-dismiss="modal">Discard</button>
                    <button @click= "createTask" type="button" class="btn btn-success">Publish</button>
                </div>
                </div>
            </div>
            </div>

        <!-- update Modal -->
            <div class="modal fade" id="updateModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
            <div class="modal-dialog">
                <div class="modal-content bg-dark text-white">
                <div class="modal-header">
                    <h5 class="modal-title" id="exampleModalLabel">Update Post</h5>
                    <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                </div>
                <div class="modal-body">

                    <div class="alet alert-danger" v-if="errors.length > 0">
                        <ul>
                            <li v-for="error in errors">{{error}}</li>
                        </ul>
                    </div>

                    <div class="form-group">
                        <label for="name">Post Title</label>
                        <input v-model="new_update_task.name" type="text" id="name" class="form-control">
                    </div>
                    <div class="form-group">
                        <label for="description">Description</label>
                        <input v-model="new_update_task.body" type="text" id="description" class="form-control">
                    </div>
                    <form action="/file-upload" class="form-control dropzone" id="dropzone">
                        <div class="fallback">
                            <input name="file" type="file" multiple />
                        </div>
                    </form>
                    
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-outline-danger" data-bs-dismiss="modal">Discard</button>
                    <button @click= "updateTask" type="button" class="btn btn-success">Update Post</button>
                </div>
                </div>
            </div>
            </div>
            
        

        

    </div>
    
</template>


<script src="../../assets/js/plugins/dropzone.min.js"></script>
<script>
    export default {

        data(){
            return {
                task:{
                    name:'',
                    body:'',
                    file_path:'',
                },

                tasks: [],

                uri: 'http://post.test/tasks/',

                errors: [],
                new_update_task: [],
                image: 'images/loader.gif',
                loading: false

            }
        },

        methods: {

                loadUpdateModal(index){
                    this.errors = [];
                    $("#updateModal").modal("show");
                    this.new_update_task = this.task[index];
                },

                deleteTask(index){
                    let confirmBox = confirm("Do you really want to delete this post?");
                    if (confirmBox == true){
                        axios.delete(this.uri+this.tasks[index].id)
                            .then(response=>{
                                this.$delete(this.tasks, index);
                            }).catch(error=>{
                                console.log("Could not delete for some reason")
                            });
                    }
                }
,
                createTask(){
                    axios.post(this.uri, {name: this.task.name, body: this.task.body, file_path: this.task.file_path}).then(response=>{
                        this.tasks.push(response.data.task);
                    }).catch(error=>{

                        this.errors = [];

                        if(error.response.data.errors.name){
                            this.errors.push(error.response.data.errors.name[0])
                        }
                        if(error.response.data.errors.body){
                            this.errors.push(error.response.data.errors.body[0])
                        }
                        if(error.response.data.errors.file_path){
                            this.errors.push(error.response.data.errors.file_path[0])
                        }

                    });
                },

                updateTask(){
                    axios.patch(this.uri+this.new_update_task.id, {
                        name: this.new_update_task.name,
                        body: this.new_update_task.body,
                        file_path: this.new_update_task.file_path,

                    }).then(response =>{
                        $("#updateModal").modal("hide")
                    }).catch(error=>{
                        if(error.response.data.errors.name){
                            this.errors.push(error.response.data.errors.name[0])
                        }
                        if(error.response.data.errors.body){
                            this.errors.push(error.response.data.errors.body[0])
                        }
                        if(error.response.data.errors.file_path){
                            this.errors.push(error.response.data.errors.file_path[0])
                        }
                    });
                },

                loadTasks(){
                axios.get(this.uri).then(response=>{

                        this.tasks = response.data.tasks;
                        this.loading = true

                })
            }
        },


        mounted() {
            this.loadTasks();
        }
    }
</script>
