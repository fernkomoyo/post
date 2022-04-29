<template>
    
    <div>
        <div v-if="!loading">
            <img class="rounded mx-auto d-block" src="image" alt="loader">
        </div>
        

        <!-- presentacion de bootstrap cards!!! -->
        <div class="card-deck">
            <div class="row">
                <!-- Boton para crear con bootstrap card -->
                <div class="card bg-transparent text-white" style="width: 18rem;">
                    
                    <a href="#"  class="btn stretched-link" type="button" data-bs-toggle="modal" data-bs-target="#createModal">
                        <img class="card-img-top" src="https://i.imgur.com/qEXri9r.png" alt="Card image cap">
                    </a>
                </div>

            <div class="col-md-4 col-6 my-1 d-flex align-items-stretch" v-for="(task, index) in tasks">
                <div class="card bg-dark text-white" style="width: 18rem;">
                <img class="card-img-top" :src="task.file_path" alt="Card image cap">
                <div class="card-body">
                    <h5 class="card-title">{{task.name}}</h5>
                    <p class="card-text">{{task.body }}</p>
                    <hr>
                   <!-- <button @click="loadUpdateModal(index)" type="button" class="btn btn-outline-warning w-20" data-bs-toggle="modal" data-bs-target="#updateModal">Edit</button> -->
                    <button @click="deleteTask(index)" class="btn btn-outline-danger btn-sm w-100 ">Delete Post</button>
                </div>
                </div>
            </div>
            </div>
        </div>       


        <!-- Create Modal -->
            <div class="modal fade" id="createModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
            <div class="modal-dialog">
                <div class="modal-content bg-black text-white">
                <div class="modal-header">
                    
                    <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                </div>
                <h2 class="modal-title text-center" id="exampleModalLabel">Create Post</h2>
                <div class="modal-body ">

                    <div class="alet alert-danger" v-if="errors.length > 0">
                        <ul>
                            <li v-for="error in errors">{{error}}</li>
                        </ul>
                    </div>

                    <div class="form-group border-white">
                        <label for="name">Post Title</label>
                        <input v-model="task.name" type="text" id="name" class="form-control">
                    </div>
                    <div class="form-group">
                        <label for="description">Description</label>
                        <input v-model="task.body" type="text" id="description" class="form-control">
                    </div>
                    <div @dragenter.prevent="toggleActive" @dragleave.prevent="toggleActive" @dragover.prevent @drop.prevent="toggleActive"
                        :class="{'active-dropzone': active}"   class="center">
                        <label for="description">Add Image</label>
                        <label for="dropzoneFile">
                            <form action="/file-upload" class="form-control dropzone" id="dropzone">
                                <img src="https://i.imgur.com/sY5R8uI.png" alt="upload logo">
                                <span class="">Upload or drag and drop .PDF file here</span>
                                <input id="dropzoneFile" type="file" multiple class="input-toggle-off"/>
                            </form>
                        </label>
                    </div>
                    
                     
                    
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-sm btn-outline-danger w-50" data-bs-dismiss="modal">Discard</button>
                    <button @click= "createTask" type="button" class="btn  btn-sm btn-success w-50">Publish</button>
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
