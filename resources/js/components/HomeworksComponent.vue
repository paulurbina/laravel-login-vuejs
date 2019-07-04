<template>
    <div>
        <h3>Test template</h3>
        <form @submit.prevent="adding">
            <div class="form-group">
                <input type="text" class="form-control" placeholder="Name" v-model="homework.nombre">
            </div>
            <div class="form-group">
                <input type="text" class="form-control" placeholder="Description" v-model="homework.descripcion">
            </div>
            <div class="form-group">
                <button type="submit" class="btn btn-primary">Adding</button>
            </div>
        </form>

        <hr>

        <h3>List Notes</h3>

        <ol class="list-group">
            <li class="list-group-item list-group-item-secondary"
                v-for="(item, index) in homeworks" :key="index">
                <p>{{item.nombre}}</p>
                <p>{{item.descripcion}}</p>
            </li>
        </ol>

    </div>    
</template>


<script>
export default {
    data() {
        return {
            homeworks: [],
            homework: {nombre: '', descripcion: ''}
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
            console.log(this.homework.nombre, this.homework.descripcion);
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
         
        }
    }
}
</script>
