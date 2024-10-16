<template>
  <q-page>
    
    <body>
      <div class="container">
      <div class="header">
        <span>Cadastro de unidade de medida</span>
        <q-btn push color="primary" label="Nova medida" />
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
          <q-input outlined v-model="text" label="Sigla*" required/>
          <q-input outlined v-model="text" label="Descricao*" required/>
          <q-btn push color="primary" label="CADASTRAR" />
        </q-form>
      </div>
    </div>

    </div>
    </body>
  



  </q-page>
</template>

<script>
import { defineComponent } from 'vue'

export default defineComponent({
  name: 'IndexPage'
})
</script>
