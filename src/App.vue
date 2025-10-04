<script setup>
import { onMounted, ref } from 'vue';
import Matrix from './components/Matrix.vue';

const items = ref(new Map());
const clients = ref([]);
const loading = ref(true);
const modalVisible = ref(false);
const modalName = ref('');

onMounted(async () => {
  try {
    const response = await fetch(
      `${import.meta.env.VITE_CO_API_BASE_URL}/clients/categories`
    );
    if (!response.ok) throw new Error('Erro ao carregar API');
    const values = await response.json();
    const data = values.data;

    items.value = new Map(data.map(item => [item.code, item.total]));
  } catch (err) {
    console.error(err);
  } finally {
    loading.value = false;
  }
});

async function handleMouseClick(cell) {
  try {
    const response = await fetch(
      `${import.meta.env.VITE_CO_API_BASE_URL}/clients/categories/${cell.key}`
    );
    if (!response.ok) throw new Error('Erro ao carregar detalhes dos clientes');
    const values = await response.json();

    clients.value = values.data;
    modalName.value = `Detalhes do Cliente - ${cell.text}`;
    modalVisible.value = true;
  } catch (err) {
    console.error(err);
  }
}
</script>

<template>
  <main class="rfm">
    <h1>Análise RFM</h1>

    <div class="content">
      <Matrix v-if="!loading" :data="items" :onCellClick="handleMouseClick" />
    </div>

    <section class="client-modal" v-if="modalVisible">
      <div class="modal-content">
        <header>
          <h2>{{ modalName }}</h2>
          <button class="close-button" @click="modalVisible = false">X</button>
        </header>

        <section class="modal-body">
          <table>
            <thead>
              <tr>
                <th>#</th>
                <th>Nome</th>
                <th>Recência</th>
                <th>Frequência</th>
                <th>Total Gasto</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(client, index) in clients" :key="client.id">
                <td>{{ index + 1 }}</td>
                <td>{{ client.name }}</td>
                <td>{{ client.recency }}</td>
                <td>{{ client.frequency }}</td>
                <td>{{ (client.total_spent / 100).toLocaleString('pt-BR', { style: 'currency', currency: 'BRL' }) }}</td>
              </tr>
            </tbody>
          </table>
        </section>
      </div>
    </section>
  </main>
</template>

<style scoped>
.rfm {
  width: 100vw;
  height: 100vh;
  padding: 32px;
  display: flex;
  flex-direction: column;
  align-items: center;
  background-color: #005aa1;
  font-family: sans-serif;
  color: #fff;
}

h1 {
  margin-bottom: 16px;
}

.content {
  width: 800px;
  height: 800px;
  display: flex;
  gap: 16px;
}

.client-modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.modal-content {
  background-color: #fff;
  padding: 20px 20px 0 20px;
  border-radius: 8px;
  max-width: 90%;
  width: 800px;
  height: 500px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
  position: relative;
  color: #000;
}

.modal-content header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
}

.close-button {
  background: none;
  border: none;
  font-size: 20px;
  cursor: pointer;
}

.modal-body {
  overflow-y: auto;
  max-height: 80%
}

.modal-body table {
  width: 100%;
  border-collapse: collapse;
}

.modal-body th,
.modal-body td {
  border: 1px solid #ddd;
  padding: 8px;
  text-align: left;
}

.modal-body th {
  background-color: #f2f2f2;
}

.modal-body tr:nth-child(even) {
  background-color: #f9f9f9;
}
</style>
