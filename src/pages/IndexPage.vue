<template>
  <q-page>
    <body>
      <div class="container">
        <div class="header">
          <span>Cadastro de unidade de medida</span>
          <q-btn push color="cyan-10" label="Nova medida" @click="openModal(false)" />
        </div>

        <q-input
          v-model="search"
          label="Pesquisar por Sigla"
          outlined
          class="q-mt-md"
        />

        <div class="divTable">
          <table>
            <thead>
              <tr>
                <th>ID</th>
                <th>Código ERP</th>
                <th>Sigla</th>
                <th>Descrição</th>
                <th>Status</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(item, index) in filteredItens" :key="index" @click="openModal(true, index)">
                <td>{{ item.id }}</td>
                <td>{{ item.codigoERP }}</td>
                <td>{{ item.sigla }}</td>
                <td>{{ item.descricao }}</td>
                <td>{{ item.status }}</td>
              </tr>
            </tbody>
          </table>
        </div>

        <q-dialog v-model="modalVisible">
          <q-card>
            <q-card-section>
              <q-form @submit.prevent="saveItem"> <!-- Prevent default form submission -->
                <q-input
                  v-model="form.codigoERP"
                  label="Código ERP"
                  outlined
                  maxlength="5"
                  :rules="[(val) => val === '' || /^[a-zA-Z0-9]*$/.test(val) || 'Deve ser alfanumérico']"
                />
                <q-input v-model="form.sigla" label="Sigla*" outlined required />
                <q-input v-model="form.descricao" label="Descrição*" outlined required />

                <q-toggle
                  v-if="id !== undefined"
                  v-model="form.status"
                  label=""
                  :checked-icon="form.status === 'Ativo' ? 'check_circle' : 'cancel'"
                  :unchecked-icon="form.status === 'Inativo' ? 'cancel' : 'check_circle'"
                  :color="form.status === 'Ativo' ? 'green' : 'grey'"
                  :true-value="'Ativo'"
                  :false-value="'Inativo'"
                  class="q-mt-md"
                >
                  {{ form.status === 'Ativo' ? 'Ativo' : 'Inativo' }}
                </q-toggle>

                <div class="q-mt-md">
                  <q-btn type="submit" push color="primary" label="CADASTRAR" />
                  <q-btn push color="negative" label="Cancelar" @click="closeModal" />
                </div>
              </q-form>
            </q-card-section>
          </q-card>
        </q-dialog>
      </div>
    </body>
  </q-page>
</template>

<script>
import { defineComponent, ref, onMounted, computed } from 'vue';

export default defineComponent({
  name: 'IndexPage',
  setup() {
    const modalVisible = ref(false);
    const form = ref({
      id: null,
      codigoERP: '',
      sigla: '',
      descricao: '',
      status: 'Ativo',
    });
    const itens = ref([]);
    const id = ref(undefined);
    const search = ref('');

    const generateRandomId = () => Math.floor(Math.random() * 1000000);

    const openModal = (edit = false, index = 0) => {
      modalVisible.value = true;
      if (edit) {
        form.value = { ...itens.value[index] };
        id.value = index;
      } else {
        form.value = {
          id: generateRandomId(),
          codigoERP: '',
          sigla: '',
          descricao: '',
          status: 'Ativo',
        };
        id.value = undefined;
      }
    };

    const closeModal = () => {
      modalVisible.value = false;
    };

    const saveItem = () => {
      // Verificar se a sigla já existe
      const existingItem = itens.value.find(item => item.sigla.toUpperCase() === form.value.sigla.toUpperCase());
      if (existingItem && id.value === undefined) {
        alert('A sigla já existe. Por favor, insira uma sigla diferente.');
        return;
      }

      // Converte a sigla e a descrição para caixa alta
      form.value.sigla = form.value.sigla.toUpperCase();
      form.value.descricao = form.value.descricao.toUpperCase();

      if (id.value !== undefined) {
        itens.value[id.value] = { ...form.value };
      } else {
        itens.value.push({ ...form.value });
      }
      setItensBD();
      closeModal();
    };

    const loadItens = () => {
      itens.value = getItensBD();
    };

    const getItensBD = () => JSON.parse(localStorage.getItem('dbUnidades')) ?? [];
    const setItensBD = () => localStorage.setItem('dbUnidades', JSON.stringify(itens.value));

    const filteredItens = computed(() => {
      return itens.value.filter(item => 
        item.sigla.toLowerCase().includes(search.value.toLowerCase())
      );
    });

    onMounted(() => {
      loadItens();
    });

    return {
      modalVisible,
      form,
      itens,
      openModal,
      closeModal,
      saveItem,
      id,
      search,
      filteredItens,
    };
  },
});
</script>

<style scoped>
.container {
  padding: 20px;
}
.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.divTable {
  margin-top: 20px;
}
</style>