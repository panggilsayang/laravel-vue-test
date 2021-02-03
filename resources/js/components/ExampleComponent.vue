<template>
    <div class="container mt-5">
        <div class="row">
            <div class="col-3">
                Date: {{new Date().toLocaleDateString()}}
            </div>
            <div class="col-6 text-center">
                <span class="h1"><u>"My Awesome List"</u></span>
            </div>
        </div>
        <div class="row">
            <div class="col-6"># Add User</div>
            <div class="col-6 text-right">By {{creatorName}}</div>
        </div>
        <div class="row">
            <div class="col-12 card pt-3 pb-3">
                <form class="form-inline">
                    <input type="text" class="form-control mb-2 mr-sm-2" placeholder="Name" v-model="name" required>
                    <input type="text" class="form-control mb-2 mr-sm-2" placeholder="Email" v-model="email" required>
                    <button type="submit" class="btn btn-primary mb-2" @click="save">Submit</button>
                </form>
            </div>
            <div class="col-12 m-2"># User</div>
            <div class="col-12">
                <div class="table-responsive table-wrapper-scroll-y my-custom-scrollbar">
                <table class="table table-bordered">
                    <thead>
                        <tr>
                            <th>Name</th>
                            <th>Email</th>
                            <th class="text-center">Action</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="(user,index) in users" :key="index">
                            <td>{{user.name}}</td>
                            <td>{{user.email}}</td>
                            <td class="text-center">
                                <button type="button" class="btn btn-info" @click="showData(user)">Update</button>
                                <button type="button" class="btn btn-danger" @click="deleteData(user)">Delete</button>
                            </td>
                        </tr>
                    </tbody>
                </table>
                </div>
            </div>
        </div>

        <!-- Modal -->
        <div class="modal fade" id="exampleModal" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel" aria-hidden="true">
        <div class="modal-dialog" role="document">
            <div class="modal-content">
            <div class="modal-header">
                <h5 class="modal-title" id="exampleModalLabel">Edit User</h5>
                <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                <span aria-hidden="true">&times;</span>
                </button>
            </div>
            <form>
            <div class="modal-body">
                    <div class="form-group">
                        <label for="name">Name</label>
                        <input type="text" name="form-control" v-model="data.name">
                    </div>
                    <div class="form-group">
                        <label for="name">Email</label>
                        <input type="text" name="form-control" v-model="data.email">
                    </div>
            </div>
            </form>
            <div class="modal-footer">
                <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
                <button type="button" class="btn btn-primary" @click="updateData">Save changes</button>
            </div>
            </div>
        </div>
        </div>

    </div>
</template>

<script>
    export default {
        data() {
            return {
                creatorName: 'Nur Muhammad Umar Ihsani',
                name: '',
                email: '',
                id: null,
                data: {
                    id: null,
                    name: 'aa',
                    email: '',
                },
                users: [],
            }
        },
        mounted() {
            this.load()
        },
        methods: {
            save() {
                axios.post('api/users',{
                    name: this.name,
                    email: this.email,
                }).then((response) => {
                    alert(response.data.message)
                    this.resetAddForm()
                    this.load()
                }).catch((err) => {
                    alert('Failed to save data')
                })
            },
            resetAddForm() {
                this.name = ''
                this.email = ''
            },
            resetUpdateForm() {
                this.data = {
                    name: '',
                    email: '',
                    id: null
                }
            },
            load() {
                axios.get('api/users').then((response) => {
                    this.users = response.data.data
                }).catch((err) => {
                    console.log(err);
                    alert('Failed fetch data, try refresh page');
                })
            },
            showData(user) {
                this.data.name = user.name
                this.data.email = user.email
                this.data.id = user.id
                console.log(this.data);
                $('#exampleModal').modal('show')                
            },
            updateData() {
                axios.put('api/users/'+this.data.id,this.data).then((response) => {
                    this.resetUpdateForm()
                    $('#exampleModal').modal('hide')
                    this.load()
                }).catch((err) => {
                    console.log(err);
                    alert('Failed to update data');
                })
            },
            deleteData(user) {
                if(!confirm('Are you sure want to delete this data?')) {
                    return false
                }
                axios.delete('api/users/'+user.id).then((response) => {
                    this.load()
                }).catch((err) => {
                    console.log(err);
                    alert('Failed to delete data, please try again')
                })
            }
        }
    }
</script>
<style lang="css" scoped>
.my-custom-scrollbar {
    position: relative;
    max-height: 30em;
    overflow: auto;
}
.table-wrapper-scroll-y {
    display: block;
}
</style>
