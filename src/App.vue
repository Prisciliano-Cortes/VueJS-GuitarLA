<script setup>
    import { ref, onMounted, watch } from 'vue'
    import { db } from './data/guitars.js'
    import Guitar from './components/Guitar.vue'
    import Header from './components/Header.vue'
    import Footer from './components/Footer.vue'

    const guitars = ref([])
    const cart = ref([])
    const guitar = ref({})

    watch(cart, () => {
        saveLocalStorage()
    }, {
        deep: true
    })

    onMounted( () => {
        guitars.value = db
        guitar.value = db[3]

        const cartStorage = localStorage.getItem('cart')

        if ( cartStorage ) {
            cart.value = JSON.parse(cartStorage)
        }
    })

    const saveLocalStorage = () => {
        localStorage.setItem('cart', JSON.stringify(cart.value))
    }

    const addCart = (guitar) => {
        const existCart = cart.value.findIndex( product => product.id === guitar.id)
        
        if (existCart >= 0) {
           cart.value[ existCart ].cantidad++
        } else {
            guitar.cantidad = 1
            cart.value.push(guitar)
        }
    }

    const decrementQuantity = (id) => {
        const positionCart = cart.value.findIndex( product => product.id === id)
        
        if (cart.value[positionCart].cantidad <= 1) {
            return
        }

        cart.value[ positionCart ].cantidad--
    }

    const incrementQuantity = (id) => {
        const positionCart = cart.value.findIndex( product => product.id === id)

        if (cart.value[positionCart].cantidad >= 5) {
            return
        }

        cart.value[ positionCart ].cantidad++
    }

    const deleteProduct = (id) => {
        cart.value = cart.value.filter( product => product.id !== id )
    }

    const removeCart = () => {
        cart.value = []
    }

</script>

<template>
    <Header 
        :cart="cart"
        :guitar="guitar"
        @add-cart="addCart"
        @remove-cart="removeCart"
        @delete-product="deleteProduct"
        @increment-quantity="incrementQuantity"
        @decrement-quantity="decrementQuantity"
    />

    <main class="container-xl mt-5">
        <h2 class="text-center">Nuestra Colección</h2>

        <div class="row mt-5">
            <Guitar 
                v-for="guitar in guitars"
                :key="guitar.id"
                :guitar="guitar"
                @add-cart="addCart"
            />
        </div>
    </main>

    <Footer />
</template>
