<template>
  <nav class="navbar navbar-expand-lg navbar-light bg-light shadow-sm">
    <div class="container-fluid">
      <a class="navbar-brand fw-bold text-primary" href="#">Correito</a>
      <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
        <span class="navbar-toggler-icon"></span>
      </button>
      <div class="collapse navbar-collapse" id="navbarSupportedContent">
        <ul class="navbar-nav me-auto mb-2 mb-lg-0">
          <li class="nav-item">
            <router-link class="nav-link active text-dark" aria-current="page" to="/">Home</router-link>
          </li>
          <li class="nav-item">
            <router-link v-if="existeUsuario" class="nav-link text-dark" aria-current="page" to="/about">About</router-link>
          </li>
        </ul>
        <form class="d-flex me-3">
          <input class="form-control me-2" type="search" placeholder="Buscar" aria-label="Search">
          <button class="btn btn-outline-success" type="submit">Buscar</button>
        </form>
        <!-- Iniciar sesión -->
        <button v-if="!existeUsuario" type="button" class="btn btn-outline-primary mx-2" data-bs-toggle="modal" data-bs-target="#login">
          Iniciar sesión
        </button>
        <!-- Cerrar sesión -->
        <button v-if="existeUsuario" class="btn btn-outline-danger mx-2" @click="signout">
          Cerrar sesión
        </button>
        <!-- Registrarse -->
        <button v-if="!existeUsuario" type="button" class="btn btn-outline-warning" data-bs-toggle="modal" data-bs-target="#registro">
          Regístrate
        </button>
      </div>
    </div>
  </nav>

  <!-- Modal - Registro -->
  <div class="modal fade" id="registro" tabindex="-1">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title">Regístrate</h5>
          <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
        </div>
        <div class="modal-body">
          <form @submit.prevent="register(email, password)">
            <!-- Correo -->
            <div class="input-group mb-3">
              <span class="input-group-text">Correo</span>
              <input v-model="email" type="email" class="form-control" required>
            </div>
            <!-- Contraseña -->
            <div class="input-group mb-3">
              <span class="input-group-text">Contraseña</span>
              <input v-model="password" type="password" class="form-control" required>
            </div>
            <!-- Repetir contraseña -->
            <div class="input-group mb-3">
              <span class="input-group-text">Repite contraseña</span>
              <input v-model="repassword" type="password" class="form-control" required>
            </div>
            <div class="d-grid gap-2">
              <button type="submit" class="btn btn-primary" data-bs-dismiss="modal">Registrar</button>
            </div>
          </form>
        </div>
      </div>
    </div>
  </div>

  <!-- Modal - Iniciar sesión -->
  <div class="modal fade" id="login" tabindex="-1">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title">Iniciar sesión</h5>
          <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
        </div>
        <div class="modal-body">
          <form @submit.prevent="login(email, password)">
            <!-- Correo -->
            <div class="input-group mb-3">
              <span class="input-group-text">Correo</span>
              <input v-model="email" type="email" class="form-control" required>
            </div>
            <!-- Contraseña -->
            <div class="input-group mb-3">
              <span class="input-group-text">Contraseña</span>
              <input v-model="password" type="password" class="form-control" required>
            </div>
            <div class="d-grid gap-2">
              <button type="submit" class="btn btn-primary" data-bs-dismiss="modal">Iniciar sesión</button>
            </div>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { getAuth, signOut, createUserWithEmailAndPassword, signInWithEmailAndPassword } from "firebase/auth";
import { mapGetters } from 'vuex';

export default {
  name: 'Navbar',
  data() {
    return {
      email: '',
      password: '',
      repassword: '',
      errorMessage: ''
    };
  },
  methods: {
    register(email, password) {
      const auth = getAuth();
      createUserWithEmailAndPassword(auth, email, password)
        .then((userCredential) => {
          alert('¡Registrado!');
        })
        .catch((error) => {
          this.errorMessage = error.message;
          alert(this.errorMessage);
        });
    },
    login(email, password) {
      const auth = getAuth();
      signInWithEmailAndPassword(auth, email, password)
        .then(() => {
          alert('¡Sesión iniciada!');
        })
        .catch((error) => {
          this.errorMessage = error.message;
          alert(this.errorMessage);
        });
    },
    signout() {
      const auth = getAuth();
      signOut(auth)
        .then(() => {
          alert('¡Sesión cerrada!');
        })
        .catch((error) => {
          alert(error.message);
        });
    }
  },
  computed: {
    ...mapGetters(['existeUsuario'])
  }
};
</script>

<style>
.navbar {
  background-color: #f8f9fa;
}

.navbar-brand {
  font-size: 1.5rem;
  color: #007bff !important;
}

.navbar-nav .nav-link {
  font-weight: bold;
}

.btn-outline-primary {
  border-color: #007bff;
}

.btn-outline-danger {
  border-color: #dc3545;
}

.btn-outline-warning {
  border-color: #ffc107;
}
</style>
