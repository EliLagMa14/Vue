<template>
    <div class="grid">
        <div class="col-12">
            <div class="card">
                <panel header="PUNTO DE VENTA" style="height: 100%;">
                    <di class="col-12">
                    <div class="card">
                        <div class="flex justify-content-between flex-column sm:flex-row">
                            <InputText v-model="nombreProd" type="text" placeholder="Nombre del Producto..." />
                            <InputNumber v-model="cantidad" placeholder="Cantidad" />
                            <InputNumber v-model="preciosU" :minFractionDigits="2" :maxFractionDigits="5"
                                placeholder="$ Precio U." />
                            <br>
                            <Button type="button" icon="pi pi-check" label="Registrar" severity="help"
                                @Click="RegistrarV" />
                        </div>
                    </div>
                </di>
                <DataTable :value="datos" showGridlines paginator :rows="5" tableStyle="min-width: 50rem">
                    <!-- :rowsPerPageOptions="[5, 10, 20, 50]" -->
                    <Column field="CNS" header="ID" sortable style="width: 20px"/>
                    <Column field="nombre" header="NOMBRE PRODUCTO" style="width: 550px"/>
                    <Column field="precio" header="PRECIO"  style="width:110px"/>
                    <Column field="cantidad" header="CANTIDAD" style="width:110px"/>
                    <Column field="preciosP" header="IMPORTE" style="width:110px"/>
                    <Column header="EditarP" :exportable="false" style="min-width:8rem">
                        <template #body="slotProps">
                            <Button icon="pi pi-pencil" outlined rounded class="mr-2"
                                @click="editProduct(slotProps.data)" />
                            <Button icon="pi pi-trash" outlined rounded severity="danger"
                                @click="confirmDeleteProduct(slotProps.data)" />
                        </template>
                    </Column>
                </DataTable>

                <br>
				<Toolbar class="p-mb-4">
				<template v-slot:start>
								
				</template>
				<template v-slot:end>
                    <label for="apepat">Subtotal:</label>
                        <InputText id="subtotal1" type="text" v-model="subtotal" readonly />
                        <label for="apepat">IVA (16%):</label>
                        <InputText id="iva1" type="text" v-model="iva" readonly />
                        <label for="apepat">Total:</label>
                        <InputText id="total1" type="text" v-model="total" readonly />
				</template>
				</Toolbar>
                </panel>

                <Dialog v-model:visible="productDialog" :style="{ width: '450px' }" header="Detalles del Producto"
                    :modal="true" class="p-fluid">

                    <div class="field">
                        <label for="name">Nombre del Producto</label>
                        <InputText v-model="selectedProduct.nombre" type="text" placeholder="Nombre del Producto..." />
                    </div>
                    <div class="field">
                        <label for="cantidad">Cantidad</label>
                        <InputNumber v-model="selectedProduct.cantidad" placeholder="Cantidad" />
                    </div>
                    <div class="field">
                        <label for="preciosU">Precios U.</label>
                        <InputNumber v-model="selectedProduct.precio" placeholder="$ Precio U." :minFractionDigits="2"
                            :maxFractionDigits="5" />
                    </div>
                    <template #footer>
                        <Button label="Save" icon="pi pi-bookmark" text @click="saveProduct" />
                    </template>
                </Dialog>
                <Toast/>
			
            </div>
        </div>
    </div>
</template>
<script>
import { defineComponent } from 'vue';
export default defineComponent({
    data() {
        return {
            productDialog: false,
            datos: [],
            cns: 1,
            nombreProd: '',
            subtotal: 0,
            iva: 0,
            total: 0,
            cantidad: '',
            preciosU: '',
            mensaje: 'Tienes campos vacios',
            selectedProduct: null
        };
    },
    methods: {
        RegistrarV() {
            if (this.nombreProd === '' || this.cantidad === '' || this.preciosU === '') {
                this.$toast.add({severity:'error', summary: 'Error', detail: 'Tienes Campos Vacios', life: 3000});
            } else {
                const producto = {
                    CNS: this.cns++,
                    nombre: this.nombreProd,
                    cantidad: this.cantidad,
                    precio: this.preciosU,
                    preciosP: this.preciosU * this.cantidad
                };
                this.datos.push(producto);
                this.nombreProd = '';
                this.cantidad = '';
                this.preciosU = '';
                this.sumarCantidad();
                this.$toast.add({severity:'success', summary: 'Confirmación', detail: 'Nueva compra registrada', life: 3000});
            }
        },
        sumarCantidad() {
            let suma = 0;
            for (let i = 0; i < this.datos.length; i++) {
                const producto = this.datos[i];
                suma += producto.preciosP;
            }
            const iva = suma * 0.16;
            const total = suma + iva;
            this.subtotal = suma;
            this.iva = iva;
            this.total = total;
        },
        editProduct(producto) {
            this.selectedProduct = producto;
            this.productDialog = true;
            this.selectedProduct.preciosP = this.selectedProduct.precio * this.selectedProduct.cantidad;
        },
        confirmDeleteProduct(producto) {
            const index = this.datos.indexOf(producto);
            if (index >= 0) {
                this.datos.splice(index, 1);
                this.sumarCantidad();
            }
        },
        saveProduct() {
            for (let i = 0; i < this.datos.length; i++) {
                if (this.datos[i].CNS === this.selectedProduct.CNS) {
                    this.datos[i].nombre = this.selectedProduct.nombre;
                    this.datos[i].cantidad = this.selectedProduct.cantidad;
                    this.datos[i].precio = this.selectedProduct.precio;
                    this.datos[i].preciosP = this.selectedProduct.precio * this.selectedProduct.cantidad;
                    //break;
                }
            }
            this.sumarCantidad();
            this.productDialog = false;
            this.$toast.add({severity:'info', summary: 'Confirmación', detail: 'Producto Guardado', life: 3000});
        },
    }
})
</script>


<style scoped lang="scss">
@import '@/assets/demo/styles/badges.scss';
::v-deep(.p-datatable-frozen-tbody) {
    font-weight: bold;
}
::v-deep(.p-datatable-scrollable .p-frozen-column) {
    font-weight: bold;
}
</style>