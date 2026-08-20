<template>
  <div>
    <h1>Lista de Tarefas</h1>

    <input v-model="novaTarefa" placeholder="Nova tarefa">
    <button @click="adicionar">Adicionar</button>

    <br><br>

    <button @click="filtro = 'todas'">Todas</button>
    <button @click="filtro = 'pendentes'">Pendentes</button>
    <button @click="filtro = 'concluidas'">Concluídas</button>

    <p>Total: {{ tarefas.length }}</p>
    <p>Pendentes: {{ pendentes }}</p>
    <p>Concluídas: {{ concluidas }}</p>

    <ul>
      <li v-for="tarefa in tarefasFiltradas" :key="tarefa.id">

        <span v-if="!tarefa.editando"
          :class="{ concluida: tarefa.concluida }">
          {{ tarefa.nome }}
        </span>

        <input v-else v-model="tarefa.nome">

        <button @click="concluir(tarefa)">
          {{ tarefa.concluida ? "Desfazer" : "Concluir" }}
        </button>

        <button v-if="!tarefa.editando" @click="editar(tarefa)">
          Editar
        </button>

        <button v-else @click="salvarEdicao(tarefa)">
          Salvar
        </button>

        <button @click="excluir(tarefa.id)">
          Excluir
        </button>

      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      novaTarefa: "",
      filtro: "todas",
      tarefas: JSON.parse(localStorage.getItem("tarefas")) || []
    }
  },

  computed: {
    pendentes() {
      return this.tarefas.filter(t => !t.concluida).length
    },

    concluidas() {
      return this.tarefas.filter(t => t.concluida).length
    },

    tarefasFiltradas() {
      if (this.filtro == "pendentes")
        return this.tarefas.filter(t => !t.concluida)

      if (this.filtro == "concluidas")
        return this.tarefas.filter(t => t.concluida)

      return this.tarefas
    }
  },

  methods: {
    salvar() {
      localStorage.setItem("tarefas", JSON.stringify(this.tarefas))
    },

    adicionar() {
      if (this.novaTarefa == "") return

      this.tarefas.push({
        id: Date.now(),
        nome: this.novaTarefa,
        concluida: false,
        editando: false
      })

      this.novaTarefa = ""
      this.salvar()
    },

    excluir(id) {
      this.tarefas = this.tarefas.filter(t => t.id != id)
      this.salvar()
    },

    concluir(tarefa) {
      tarefa.concluida = !tarefa.concluida
      this.salvar()
    },

    editar(tarefa) {
      tarefa.editando = true
    },

    salvarEdicao(tarefa) {
      tarefa.editando = false
      this.salvar()
    }
  }
}
</script>

<style>
.concluida {
  text-decoration: line-through;
  color: gray;
}
</style>