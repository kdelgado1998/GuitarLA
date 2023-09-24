<script setup>
import { ref, reactive, onMounted, watch } from 'vue'
import Guitarra from './components/Guitarra.vue'
import { db } from './data/guitarras'
import Header from './components/Header.vue'
import Footer from './components/Footer.vue'

//Reactive
// const state = reactive({
//   guitarras: db
// })
// onMounted(() => {
//   state.guitarras = db
// })
// console.log(state.guitarras)

//Ref
const guitarras = ref([])
// console.log(guitarras.value)
const carrito = ref([])
const guitarra = ref([])

watch(carrito, () => {
  guardarLocalStorage();
}, {
  deep: true
})

//metodo de ciclo de vida
onMounted(() => {
  guitarras.value = db
  guitarra.value = db[3]

  const carritoStorage = localStorage.getItem('carrito')
  if(carritoStorage){
    //Json.parse convierte a un arreglo el carrito de JSON stringify ya que local storage no soporta arreglos y hay que hacer toda la convercion
    carrito.value = JSON.parse(carritoStorage)
  }
})

const guardarLocalStorage = () => {
  localStorage.setItem('carrito', JSON.stringify(carrito.value))
}

const agregarCarrito = (guitarra) => {
  //Find index retorna en base a cierta condicion si existe o no, en este caso itera sobre producto y retorna true o false dependiendo de si existe o no, si no se ha agregado previamente en el carrito retorna un -1
  const existeCarrito = carrito.value.findIndex(producto => producto.id === guitarra.id)
  // console.log(existeCarrito)

  if (existeCarrito >= 0) {
    //esta linea inyecta en el carrito el campo en el arreglo en caso de que una guitarra exista, si existe solo aumenta en uno el valor de cantidad
    carrito.value[existeCarrito].cantidad++
  } else {
    guitarra.cantidad = 1
    carrito.value.push(guitarra)
  }

}

const decrementarCantidad = (id) => {
    // console.log(id)
    const index = carrito.value.findIndex(producto => producto.id === id)
    if(carrito.value[index] <= 1) return
    carrito.value[index].cantidad--
  }

  const incrementarCantidad = (id) => {
    // console.log(id)
    const index = carrito.value.findIndex(producto => producto.id === id)
    if(carrito.value[index] >= 15) return
    carrito.value[index].cantidad++
  }

  const eliminarProducto = (id) => {
    // console.log(id)
    carrito.value = carrito.value.filter(producto => producto.id !== id)
  }

  const vaciarCarrito = () => {
    carrito.value = []
  }

</script>
    <!-- @ es un evento y : es un prop -->
<template>
  <Header 
  :carrito="carrito"
  :guitarra="guitarra"
    @incrementar-cantidad="incrementarCantidad"
    @decrementar-cantidad="decrementarCantidad" 
    @agregar-carrito="agregarCarrito"
    @eliminar-producto="eliminarProducto"
    @vaciar-carrito="vaciarCarrito"
  />

  <main class="container-xl mt-5">
    <h2 class="text-center">Nuestra Colección</h2>

    <div class="row mt-5">

      <Guitarra 
      v-for="guitarra in guitarras" 
      :key="guitarra.id"
      :guitarra="guitarra" 
      @agregar-carrito="agregarCarrito" />

    </div>
  </main>

  <Footer />
</template>
