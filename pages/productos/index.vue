<template>
    <div class="container mt-5">
        <div class="card">
            <div class="card-header">
                <h4>Lista de Productos
                    <NuxtLink to="/productos/create" class="btn btn-primary float-end">Agregar Producto</NuxtLink>
                </h4>
            </div>
            <div class="card-body">
                <div v-if="isLoading">
                    <Loading title="Loading..." />
                </div>
                <div v-else>
                    <table class="table table-striped table-bordered">
                        <thead>
                            <tr>
                                <th>Id</th>
                                <th>Producto</th>
                                <th>Categoria</th>
                                <th>Stock</th>
                                <th>IVA</th>
                                <th>Precio</th>
                                <th>Created At</th>
                                <th>Action</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-for="(item, index) in producto" :key="index">
                                <td>{{ item.id }}</td>
                                <td>{{ item.producto }}</td>
                                <td>{{ item.categoria }}</td>
                                <td>{{ item.stock }}</td>
                                <td>{{ formatIva(item.iva) }}</td>
                                <td>{{ formatCurrency(item.price) }}</td>
                                <td>{{ item.created_at }}</td>
                                <td>
                                    <NuxtLink :to="`/productos/${item.id}`" class="btn btn-success btn-sm mx-2">Edit</NuxtLink>
                                    <button type="button" @click="confirmDelete(item.id)" class="btn btn-danger btn-sm mx-2">Delete</button>
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
import Swal from 'sweetalert2';

export default {
    name: 'producto',
    data() {
        return {
            producto: [],
            isLoading: true,
        };
    },
    mounted() {
        this.getProducto();
    },
    methods: {
        // Método para formatear el precio con el símbolo $
        formatCurrency(value) {
            const amount = Number(value);
            return isNaN(amount) ? '$ 0.00' : `$ ${amount.toFixed(2)}`;
        },
        // Método para formatear el IVA con el símbolo %
        formatIva(value) {
            const iva = Number(value);
            return isNaN(iva) ? '0%' : `${iva}%`;
        },

        getProducto() {
            this.isLoading = true;

            axios.get('http://localhost:8000/api/producto').then((res) => {
                this.isLoading = false;
                this.producto = res.data.producto;
            });
        },

        confirmDelete(productoId) {
            // Mostrar alerta de confirmación
            Swal.fire({
                title: '¿Estás seguro?',
                text: '¡No podrás revertir esta acción!',
                icon: 'warning',
                showCancelButton: true,
                confirmButtonColor: '#3085d6',
                cancelButtonColor: '#d33',
                confirmButtonText: 'Sí, eliminar',
                cancelButtonText: 'Cancelar'
            }).then((result) => {
                if (result.isConfirmed) {
                    this.deleteProducto(productoId);
                }
            });
        },

        deleteProducto(productoId) {
            axios.delete(`http://localhost:8000/api/producto/${productoId}/delete`)
                .then(() => {
                    this.getProducto(); // Actualizar la lista después de eliminar
                    Swal.fire(
                        '¡Eliminado!',
                        'El producto ha sido eliminado.',
                        'success'
                    );
                })
                .catch((error) => {
                    console.error(error);
                    Swal.fire(
                        'Error',
                        'Hubo un problema al eliminar el producto.',
                        'error'
                    );
                });
        }
    },
};
</script>

<style scoped>
</style>
