<template>
    <div>
        <!-- form edited homework -->
        <form @submit.prevent="editHomework(homework)" v-if="editActive">
           <div class="form-group">
              <h3>Edit Homework!</h3>
           </div>
            <div class="form-group">
                <input type="text" class="form-control" placeholder="Name" v-model="homework.nombre">
            </div>
            <div class="form-group">
                <input type="text" class="form-control" placeholder="Description" v-model="homework.descripcion">
            </div>
            <div class="form-group">
                <button type="submit" class="btn btn-secondary" @click="cancelEdit()">Cancel</button>
                <button type="submit" class="btn btn-warning">Save</button>
            </div>
        </form>

        <!-- form adding homework -->
        <form @submit.prevent="adding" v-else>
            <div class="form-group">
                <h3>Adding Homework!</h3>        
            </div>
            <div class="form-group">
                <input type="text" class="form-control" placeholder="Name" v-model="homework.nombre">
            </div>
            <div class="form-group">
                <input type="text" class="form-control" placeholder="Description" v-model="homework.descripcion">
            </div>
            <div class="form-group">
                <button type="submit" class="btn btn-success">Adding</button>
            </div>
        </form>

        <hr>

        <h3>List Notes</h3>

        <ol class="list-group">
           
            <li class="list-group-item list-group-item-secondary"
                v-for="(item, index) in homeworks" :key="index">
                 <span class="bagde badge-info float-right">Create: {{item.created_at}} </span>
                <p class="badge badge-light" style="font-size: 18px;">{{item.nombre}}</p>
                <p>{{item.descripcion}}</p>
                <button class="btn btn-danger btn-sm" @click="deleteHomework(item, index)">Delete</button>
                <button class="btn btn-warning btn-sm" @click="editForm(item)">Edit</button>
            </li>
        </ol>

    </div>    
</template>

<script>
export default {
    data() {
        return {
            homeworks: [],
            homework: {nombre: '', descripcion: ''},
            editActive: false
        }
    },
    created() {
        axios.get('/notas')
            .then(res => {
                this.homeworks = res.data;
            });
    },
    methods: {
        adding() {
            // console.log(this.homework.nombre, this.homework.descripcion);
            if(this.homework.nombre.trim() === '' || this.homework.descripcion.trim() === '') {
                alert("Inputs imcomplete's!");
                return;
            }

            const params = {
                nombre: this.homework.nombre,
                descripcion: this.homework.descripcion
            }

            this.homework.nombre = '';
            this.homework.descripcion = '';

            axios.post('/notas', params)
                .then(res => {
                    this.homeworks.push(res.data);
                });
         
        },

        editForm(item) {
            this.editActive = true;
            this.homework.nombre = item.nombre;
            this.homework.descripcion = item.descripcion;
            this.homework.id = item.id;
        },

        editHomework(homework) {
            const params = {
                nombre: homework.nombre,
                descripcion: homework.descripcion
            };
            axios.put(`/notas/${homework.id}`, params)
                .then(res => {
                    this.editActive = false;
                    const index = this.homeworks.findIndex((item) => {
                        item.id = homework.id; 
                    });
                    this.homeworks[index] = res.data;
                })
        },

        deleteHomework(item, index) {
            const confir = confirm(`You want to delete the note ${item.nombre}`);
            if(confir) {
                axios.delete(`/notas/${item.id}`)
                    .then(() => {
                        this.homeworks.splice(index, 1);
                    });
            }
        },

        cancelEdit() {
            this.editActive = false;
            this.homework = {nombre: '', descripcion: ''};
        }
    }
}
</script>
