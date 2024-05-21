<script setup>
    import { ref, onMounted, watch } from 'vue';
    import {db} from './data/guitarras';
    import Guitarra from './components/Guitarra.vue';
    import Header from './components/Header.vue';
    import Footer from './components/Footer.vue';
    

    const guitarras = ref([]);
    const carrito = ref([]);
    const guitarra = ref({});

    watch(carrito,()=>{
        console.log('Escuchando carrito');
        guardarLocalStorage();
    },{
        deep:true
    });

    onMounted(()=>{
        guitarras.value = db;
        guitarra.value = db[3];
        console.log(guitarras.value);

        const carritoStorage = localStorage.getItem('carrito');
        if(carritoStorage){
            carrito.value = JSON.parse(carritoStorage);
        }
    });

    const guardarLocalStorage = ()=>{
        localStorage.setItem('carrito',JSON.stringify(carrito.value));
    }

    const agregarCarrito = (guitarra)=>{
        const existeCarrito = carrito.value.findIndex(producto => producto.id === guitarra.id);
        console.log('existeCarrito',existeCarrito);
        if(existeCarrito >= 0)
        {
            console.log('el elemento ya existe');
            carrito.value[existeCarrito].cantidad++;
        }
        else{
            console.log(guitarra)
            guitarra.cantidad = 1;
            carrito.value.push(guitarra);
        }
    }

    const decrementarCantidad = (id)=>{
        console.log('decarementar',id);
        const existeCarrito = carrito.value.findIndex(producto => producto.id === id);
        if(existeCarrito >= 0)
        {
            if(carrito.value[existeCarrito].cantidad ==1)return;
            carrito.value[existeCarrito].cantidad--;
        }

    }

    const aumentarCantidad = (id)=>{
        const existeCarrito = carrito.value.findIndex(producto => producto.id === id);
        if(existeCarrito >= 0)
        {
            if(carrito.value[existeCarrito].cantidad >=5)return;
            carrito.value[existeCarrito].cantidad++;
        }
    }

    const eliminarProducto = (id)=>{
        console.log('eliminar producto',id);
        carrito.value = carrito.value.filter(producto=>producto.id != id);
    }
    
    const vaciarCarrito = ()=>{
        carrito.value.length=0;
    }
</script>

<template>
    <div>
        <Header
            :carrito="carrito"
            :guitarra="guitarra"
            @agregar-carrito="agregarCarrito"
            @decrementar-cantidad="decrementarCantidad"
            @aumentar-cantidad="aumentarCantidad"
            @eliminar-producto="eliminarProducto"
            @vaciar-carrito="vaciarCarrito"
        />

        <main class="container-xl mt-5">
            <h2 class="text-center">Nuestra Colección</h2>

            <div class="row mt-5">
                <Guitarra 
                    v-for="guitarra in guitarras" 
                    :guitarra="guitarra"
                    @agregar-carrito="agregarCarrito"
                    />
            </div>
        </main>
        <Footer />
    </div>
</template>

<style scoped>

</style>
