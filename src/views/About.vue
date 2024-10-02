<template>
  <div class="container mt-5">
    <Navbar />
    <!-- Título del formulario -->
    <h2 class="text-center mb-4">Gestión de Usuarios</h2>
    
    <!-- ////////// Formulario Añadir ////////// -->
    <div class="row justify-content-center">
      <div class="col-md-6">
        <div class="card shadow-sm p-4">
          <form>
            <!-- Nombre -->
            <div class="mb-3">
              <label for="nombre" class="form-label">Nombre</label>
              <input
                v-model="usuario.nombre"
                type="text"
                class="form-control"
                placeholder="Ingresa el nombre"
              />
            </div>

            <!-- Correo -->
            <div class="mb-3">
              <label for="correo" class="form-label">Correo</label>
              <input
                v-model="usuario.correo"
                type="email"
                class="form-control"
                placeholder="Ingresa el correo"
              />
            </div>

            <!-- Carga de imagen -->
            <div class="mb-3">
              <label for="imagen" class="form-label">Imagen de perfil</label>
              <input type="file" class="form-control" @change="buscarImagen($event)" />
            </div>

            <!-- Botones Guardar/Actualizar -->
            <div class="d-flex justify-content-between mt-4">
              <button
                v-show="this.editar === true"
                @click.prevent="actualizarDato(id)"
                class="btn btn-warning btn-lg"
              >
                <i class="bi bi-pencil-square"></i> Actualizar
              </button>
              <button
                v-show="this.editar === false"
                @click.prevent="agregarDato()"
                class="btn btn-primary btn-lg"
              >
                <i class="bi bi-save"></i> Guardar
              </button>
            </div>

            <!-- Vista previa de la imagen -->
            <div v-if="datoImagen" class="mt-4 text-center">
              <img :src="datoImagen" class="img-thumbnail" alt="Vista previa de imagen" />
            </div>
          </form>
        </div>
      </div>
    </div>

    <!-- ////////// Tabla de Usuarios ////////// -->
    <div class="mt-5">
      <h3 class="text-center">Lista de Usuarios</h3>
      <table class="table table-hover table-striped mt-3">
        <thead class="table-dark">
          <tr>
            <th scope="col">ID</th>
            <th scope="col">Nombre</th>
            <th scope="col">Correo</th>
            <th scope="col">Editar</th>
            <th scope="col">Eliminar</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(item, index) in usuarios" :key="index">
            <th scope="row">{{ index }}</th>
            <td>{{ item.nombre }}</td>
            <td>{{ item.correo }}</td>
            <td>
              <button
                @click.prevent="obtenerDatoID(item.id); this.editar = !this.editar"
                class="btn btn-warning"
              >
                <i class="bi bi-pencil-square"></i> Editar
              </button>
            </td>
            <td>
              <button
                @click.prevent="eliminarDato(item.id)"
                class="btn btn-danger"
              >
                <i class="bi bi-trash"></i> Eliminar
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
import Navbar from '@/components/Navbar.vue';
import { collection, getDocs, addDoc, deleteDoc, doc, getDoc, updateDoc } from 'firebase/firestore/lite';
import { db, storage } from "../main";
import firebase from 'firebase/compat/app';
import router from '../router/index';

export default {
  name: 'About',
  components: {
    Navbar
  },
  data() {
    return {
      file: null,
      datoImagen: null,
      error: null,
      editar: false,
      loading: false,
      usuarios: [],
      usuario: {
        nombre: '',
        correo: '',
        foto: ''
      }
    };
  },
  methods: {
    async obtenerDatos() {
      const querySnapshot = await getDocs(collection(db, "usuarios"));
      querySnapshot.forEach((doc) => {
        let usuario = doc.data();
        usuario.id = doc.id;
        this.usuarios.push(usuario);
      });
    },
    async eliminarDato(id) {
      await deleteDoc(doc(db, "usuarios", id));
      router.go('/');
    },
    async obtenerDatoID(id) {
      const docRef = doc(db, "usuarios", id);
      const docSnap = await getDoc(docRef);
      if (docSnap.exists()) {
        this.usuario = docSnap.data();
        this.usuario.id = docSnap.id;
      } else {
        console.log("¡No existe el documento!");
      }
    },
    buscarImagen(event) {
      const tipoArchivo = event.target.files[0].type;
      if (tipoArchivo === 'image/jpeg' || tipoArchivo === 'image/png') {
        this.file = event.target.files[0];
        this.error = null;
      } else {
        this.error = 'Archivo no válido';
        this.file = null;
        return;
      }
      const reader = new FileReader();
      reader.readAsDataURL(this.file);
      reader.onload = (e) => {
        this.datoImagen = e.target.result;
      };
    },
    async agregarDato() {
      try {
        this.loading = true;
        var storageRef = firebase.storage().ref();
        await storageRef.child('imagenes').child(this.file.name).put(this.file);
        const urlDescarga = await storageRef.child('imagenes').child(this.file.name).getDownloadURL();
        await addDoc(collection(db, "usuarios"), {
          nombre: this.usuario.nombre,
          correo: this.usuario.correo,
          foto: urlDescarga
        });
        this.error = 'Imagen subida con éxito';
        this.file = null;
      } catch (error) {
        console.log(error);
      } finally {
        router.push('/');
        this.loading = false;
      }
    },
    async actualizarDato() {
      try {
        this.loading = true;
        var storageRef = firebase.storage().ref();
        await storageRef.child('imagenes').child(this.file.name).put(this.file);
        const urlDescarga = await storageRef.child('imagenes').child(this.file.name).getDownloadURL();
        const elemento = doc(db, "usuarios", this.usuario.id);
        await updateDoc(elemento, {
          nombre: this.usuario.nombre,
          correo: this.usuario.correo,
          foto: urlDescarga
        });
        this.error = 'Imagen subida con éxito';
        this.file = null;
      } catch (error) {
        console.log(error);
      } finally {
        router.push('/');
        this.loading = false;
      }
    }
  },
  mounted() {
    this.obtenerDatos();
  }
};
</script>

<style>
body {
  background-color: #f8f9fa;
}

.container {
  max-width: 960px;
  margin: 0 auto;
}

.card {
  border-radius: 12px;
  border: 1px solid #e3e6f0;
}

.table {
  border-radius: 8px;
}

.table th,
.table td {
  vertical-align: middle;
}

.btn {
  display: inline-flex;
  align-items: center;
}

.bi {
  margin-right: 5px;
}

.img-thumbnail {
  max-width: 200px;
  height: auto;
}
</style>
