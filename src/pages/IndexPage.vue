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

<style>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@200;300;400;500;700&family=Roboto:wght@100;300;400;500;700;900&family=Source+Sans+Pro:wght@200;300;400;600;700;900&display=swap');


* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: 'Poppins', sans-serif
}

#preview{
  width: 100vw;
  margin: 10px;
  padding: 10px;
  display: flex;
  align-items: end;
  justify-content: end;
}

body {
  width: 100vw;
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: rgba(0, 0, 0, 0.1)
}


button{
  cursor: pointer
}
  

.container{
  width: 90%;
  height: 80%;
  border-radius: 10px;
  background: white
}

.header {
  min-height: 70px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin: auto 12px
}
  

.header span {
  font-weight: 900;
  font-size: 20px;
  word-break: break-all
}


#new {
  font-size: 16px;
  padding: 8px;
  border-radius: 5px;
  border: none;
  color: white;
  background-color: rgb(57, 57, 226)
}


.divTable {
  padding: 10px;
  width: auto;
  height: inherit;
  overflow: auto
}


.divTable::-webkit-scrollbar {
  width: 12px;
  background-color: whitesmoke
}


.divTable::-webkit-scrollbar-thumb {
  border-radius: 10px;
  background-color: darkgray
}


table {
  width: 100%;
  border-spacing: 10px;
  word-break: break-all;
  border-collapse: collapse
}


thead {
  background-color: whitesmoke
}


tr {
  border-bottom: 1px solid rgb(238, 235, 235)!important
}
  

tbody tr td {
  vertical-align: text-top;
  padding: 6px;
  max-width: 50px
}


thead tr th {
  padding: 5px;
  text-align: start;
  margin-bottom: 50px
}

tbody tr {
  margin-bottom: 50px
}
  


@media (max-width: 700px) {
  body{
    font-size: 10px
  }
    
  
  .header span {
    font-size: 15px
  }
    

  #new {
    padding: 5px;
    font-size: 10px
  }
    

  .modal {
    width: 90%!important
  }
    

  .modal label {
    font-size: 12px!important
  }
    
}

.modal-container {
  width: 100vw;
  height: 100vh;
  position: fixed;
  top: 0;
  left: 0;
  background-color: rgba(0, 0, 0, 0.5);
  display: none;
  z-index: 999;
  align-items: center;
  justify-content: center;
}
  

.modal {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 40px;
  background-color: white;
  border-radius: 10px;
  width: 50%
}
  

.modal label {
  font-size: 14px;
  width: 100%

}
  
.modal input {
  width: 100%;
  outline: none;
  padding: 5px 10px;
  margin-bottom: 20px;
  border-top: none;
  border-left: none;
  border-right: none
}
  

.modal button {
  width: 100%;
  margin: 10px auto;
  outline: none;
  border-radius: 20px;
  padding: 5px 10px;
  border: none;
  background-color: rgb(57, 57, 226);
  color: white
}
  

.active {
  display: flex
}
  

.active .modal {
  animation: modal 0.4s
}
  
@keyframes modal {
  from {
    opacity: 0;
    transform: translate3d(0, -60px, 0)
  }

  to {
    opacity: 1;
    transform: translate3d(0, 0, 0)
  }
    
} 

</style>

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