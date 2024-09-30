<template>
<div class="mt-5 container">
    <div class="card">
        <div class="card-header">
            <h4>Editar Producto
                <NuxtLink to="/productos" class="btn btn-danger float-end">Back</NuxtLink>
            </h4>
        </div>
        <div class="card-body">
            <div v-if="isLoading">
                <Loading :title="isLoadingTitle" />
            </div>
            <div v-else>
                <form @submit.prevent="updateProducto">
                    <div class="mb-3">
                        <label>Nombre</label>
                        <input type="text" v-model="producto.name" class="form-control" />
                        <span class="text-danger" v-if="errorList.name">{{ errorList.name[0] }}</span>
                    </div>
                    <div class="mb-3">
                        <label>Categoria</label>
                        <input type="text" v-model="producto.categoria" class="form-control" />
                        <span class="text-danger" v-if="errorList.categoria">{{ errorList.categoria[0] }}</span>
                    </div>
                    <div class="mb-3">
                        <label>Precio</label>
                        <input type="text" v-model="producto.price" class="form-control" />
                        <span class="text-danger" v-if="errorList.price">{{ errorList.price[0] }}</span>
                    </div>
                    <div class="mb-3">
                        <label>IVA</label>
                        <input type="text" v-model="producto.iva" class="form-control" />
                        <span class="text-danger" v-if="errorList.iva">{{ errorList.iva[0] }}</span>
                    </div>
                    <div class="mb-3">
                        <label>Stock</label>
                        <input type="text" v-model="producto.stock" class="form-control" />
                        <span class="text-danger" v-if="errorList.stock">{{ errorList.stock[0] }}</span>
                    </div>
                    <div class="mb-3">
                        <button type="submit" class="btn btn-primary">Actualizar</button>
                    </div>
                </form>
            </div>
        </div>
    </div>
</div>
</template>

<script>
import axios from 'axios';
import Swal from 'sweetalert2';  // Importar SweetAlert2

export default {
    name: "productoEditar",
    data() {
        return {
            productoId: "",
            producto: {},
            isLoading: false,
            isLoadingTitle: 'Loading',
            errorList: {}
        };
    },
    mounted() {
        this.productoId = this.$route.params.id;
        this.getProducto(this.productoId);
    },
    methods: {
        getProducto(productoId) {
            this.isLoading = true;
            axios.get(`http://localhost:8000/api/producto/${productoId}/edit`).then(res => {
                this.isLoading = false;
                this.producto = res.data.producto;
            });
        },
        updateProducto() {
            this.isLoading = true;
            this.isLoadingTitle = "Actualizando";

            axios.put(`http://localhost:8000/api/producto/${this.productoId}/edit`, this.producto)
                .then(res => {
                    this.isLoading = false;
                    this.errorList = {};

                    // Mostrar SweetAlert2 con el mensaje de éxito
                    Swal.fire({
                        title: '¡Cambios guardados!',
                        text: 'Los cambios han sido guardados correctamente.',
                        icon: 'success',
                        confirmButtonText: 'OK'
                    });

                })
                .catch(error => {
                    console.error(error);

                    if (error.response && error.response.status === 422) {
                        this.errorList = error.response.data.errors;
                    }

                    this.isLoading = false;
                });
        }
    }
};
</script>

<style lang="scss" scoped>
</style>
