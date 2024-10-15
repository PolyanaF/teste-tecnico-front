<template>
  <q-page>
    
    
    <div class="container">
      <div class="header">
        <h3>Cadastro de unidade de medida</h3>
        <q-btn push color="primary" label="Nova medida" />
      </div>
    </div>

    <div class="divTable">
      <table>
        <thead>
          <tr>
            <th>Código ERP</th>
            <th>Sigla</th>
            <th>Descricao</th>
            <th>Status</th>
            <th class="acao">Editar</th>
            <th class="acao">Excluir</th>
            <!-- <q-btn push color="red" icon="delete" class="acao"/> -->
          </tr>
        </thead>
        <tbody>

        </tbody>
      </table>
    </div>
    <div class="modal-container">
      <div class="modal">
        <q-form>
          <q-input outlined v-model="text" label="cód ERP" />
          <q-input outlined v-model="text" label="Sigla*" />
          <q-input outlined v-model="text" label="Descricao*" />
          <q-btn push color="primary" label="CADASTRAR" />
        </q-form>
      </div>
    </div>

  </q-page>
</template>

<script>
import { defineComponent } from 'vue'

export default defineComponent({
  name: 'IndexPage'
})
</script>
