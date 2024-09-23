<template>
  <div>
    <h1>Registro de Movilidad por Empleado</h1>
    
    <!-- Formulario para agregar o editar empleados -->
    <form @submit.prevent="guardarEmpleado">
      <div>
        <label>Nombre empleado:</label>
        <input v-model="empleado.nombre" required />
      </div>
      <div>
        <label>Área:</label>
        <select v-model="empleado.area" required>
          <option value="producción">Producción</option>
          <option value="finanzas">Finanzas</option>
          <option value="contabilidad">Contabilidad</option>
        </select>
      </div>
      <div>
        <label>Tipo de vehículo:</label>
        <select v-model="empleado.tipoVehiculo" required>
          <option value="automóvil">Automóvil</option>
          <option value="moto">Moto</option>
        </select>
      </div>
      <div>
        <label>Número de placa:</label>
        <input v-model="empleado.placa" required />
      </div>
      <div>
        <label>Color del vehículo:</label>
        <input v-model="empleado.color" required />
      </div>
      <button type="submit">{{ editando ? 'Actualizar' : 'Guardar' }}</button>
    </form>

    <!-- Filtros -->
    <div>
      <input v-model="filtroNombre" placeholder="Buscar por nombre" />
      <select v-model="filtroTipoVehiculo">
        <option value="">Todos</option>
        <option value="automóvil">Automóvil</option>
        <option value="moto">Moto</option>
      </select>
    </div>

    <!-- Tabla de empleados -->
    <table>
      <thead>
        <tr>
          <th>Nombre</th>
          <th>Área</th>
          <th>Tipo de Vehículo</th>
          <th>Placa</th>
          <th>Color</th>
          <th>Acciones</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="empleado in empleadosFiltrados" :key="empleado.id">
          <td>{{ empleado.nombre }}</td>
          <td>{{ empleado.area }}</td>
          <td>{{ empleado.tipoVehiculo }}</td>
          <td>{{ empleado.placa }}</td>
          <td>{{ empleado.color }}</td>
          <td>
            <button @click="editarEmpleado(empleado)">Editar</button>
            <button @click="eliminarEmpleado(empleado.id)">Eliminar</button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  name: 'RegistroEmpleados',
  data() {
    return {
      empleados: [],
      empleado: {
        nombre: '',
        area: 'producción',
        tipoVehiculo: 'automóvil',
        placa: '',
        color: ''
      },
      filtroNombre: '',
      filtroTipoVehiculo: '',
      editando: false,
      empleadoId: null
    };
  },
  computed: {
    empleadosFiltrados() {
      return this.empleados.filter(empleado => {
        const filtroNombre = empleado.nombre.toLowerCase().includes(this.filtroNombre.toLowerCase());
        const filtroTipoVehiculo = this.filtroTipoVehiculo ? empleado.tipoVehiculo === this.filtroTipoVehiculo : true;
        return filtroNombre && filtroTipoVehiculo;
      });
    }
  },
  methods: {
    async obtenerEmpleados() {
      const res = await fetch('http://localhost:3000/empleados');
      const data = await res.json();
      this.empleados = data;
    },
    async guardarEmpleado() {
      if (this.editando) {
        await fetch(`http://localhost:3000/empleados/${this.empleadoId}`, {
          method: 'PUT',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify(this.empleado)
        });
        this.editando = false;
        this.empleadoId = null;
      } else {
        await fetch('http://localhost:3000/empleados', {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify(this.empleado)
        });
      }
      this.empleado = { nombre: '', area: 'producción', tipoVehiculo: 'automóvil', placa: '', color: '' };
      this.obtenerEmpleados();
    },
    editarEmpleado(empleado) {
      this.empleado = { ...empleado };
      this.empleadoId = empleado.id;
      this.editando = true;
    },
    async eliminarEmpleado(id) {
      await fetch(`http://localhost:3000/empleados/${id}`, {
        method: 'DELETE'
      });
      this.obtenerEmpleados();
    }
  },
  mounted() {
    this.obtenerEmpleados();
  }
};
</script>

<style scoped>
form {
  margin-bottom: 20px;
}

label {
  display: block;
  margin-bottom: 5px;
}

input, select {
  width: 100%;
  padding: 8px;
  margin-bottom: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

button {
  background-color: #4CAF50;
  color: white;
  padding: 10px 20px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

button:hover {
  background-color: #45a049;
}

table {
  width: 100%;
  border-collapse: collapse;
}

th, td {
  border: 1px solid #ccc;
  padding: 8px;
  text-align: left;
}

thead {
  background-color: #f2f2f2;
}
</style>
