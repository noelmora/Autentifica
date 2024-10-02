<template>
  <div>
    <Navbar />
    <div class="container my-5">
      <h2 class="text-center mb-5">Lista de Usuarios</h2>
      <div class="row row-cols-1 row-cols-md-3 g-4">
        <div class="col" v-for="(item, index) in usuarios" :key="index">
          <div class="card shadow-sm h-100">
            <img
              :src="item.foto"
              class="card-img-top rounded"
              alt="Foto de usuario"
            />
            <div class="card-body text-center">
              <h5 class="card-title">{{ item.nombre }}</h5>
              <p class="card-text text-muted">{{ item.correo }}</p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Navbar from '../components/Navbar.vue'
import { collection, getDocs } from 'firebase/firestore/lite';
import { db } from "../main";

export default {
  name: 'Home',
  components: { 
    Navbar,   
  },
  data() {
    return {
      usuarios: [],
      usuario: {
        nombre: '',
        correo: '',
        foto: ''
      }
    };
  },
  methods: {
    // Obtener datos de la base de datos
    async obtenerDatos() { 
      const querySnapshot = await getDocs(collection(db, "usuarios"));
      querySnapshot.forEach((doc) => {
        let usuario = doc.data();
        usuario.id = doc.id;
        this.usuarios.push(usuario);
      });
    }
  },
  mounted() {
    this.obtenerDatos();
  }
};
</script>

<style>
.card {
  border-radius: 15px;
  overflow: hidden;
  transition: transform 0.3s ease-in-out, box-shadow 0.3s ease-in-out;
}

.card:hover {
  transform: scale(1.03);
  box-shadow: 0 10px 20px rgba(0, 0, 0, 0.2);
}

.card-img-top {
  height: 200px;
  object-fit: cover;
}

.card-body {
  padding: 20px;
}

.card-title {
  font-size: 1.25rem;
  font-weight: bold;
}

.card-text {
  font-size: 1rem;
  color: #6c757d;
}
</style>
