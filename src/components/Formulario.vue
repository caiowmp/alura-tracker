<template>
  <div class="box">
    <div class="columns">
      <div
        class="column is-5"
        role="form"
        aria-label="Formulário para iniciar uma nova tarefa"
      >
        <input
          class="input"
          type="text"
          placeholder="Qual tarefa você deseja iniciar?"
          v-model="descricao"
        />
      </div>
      <div class="column is-3">
        <div class="select">
          <select v-model="idProjeto">
            <option value="">Selecione o projeto</option>
            <option
              :value="projeto.id"
              v-for="projeto in projetos"
              :key="projeto.id"
            >
              {{ projeto.nome }}
            </option>
          </select>
        </div>
      </div>
      <div class="column">
        <Temporizador @aoFinalizarTarefa="salvarTarefa" />
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { computed, defineComponent } from "vue";
import Temporizador from "./Temporizador.vue";
import { useStore } from "vuex";
import { NOTIFICAR } from "@/store/tipo-mutacoes";
import { key } from "@/store";
import { TipoNotificacao } from "@/interfaces/INotificacao";

export default defineComponent({
  name: "Formulario",
  emits: ["aoSalvarTarefa"],
  components: {
    Temporizador,
  },
  data() {
    return {
      descricao: "",
      idProjeto: "",
    };
  },
  methods: {
    salvarTarefa(tempoEmSegundos: number): void {
      const projeto = this.projetos.find((p) => p.id == this.idProjeto);
      if (!projeto) {
        this.store.commit(NOTIFICAR, {
          titulo: "Ops!",
          texto: "Selecione um projeto antes de finalizar a tarefa!",
          tipo: TipoNotificacao.FALHA,
        });
        return;
      }

      this.$emit("aoSalvarTarefa", {
        duracaoEmSegundos: tempoEmSegundos,
        descricao: this.descricao,
        projeto: this.projetos.find((proj) => proj.id == this.idProjeto),
      });
      this.descricao = "";
    },
  },
  setup() {
    const store = useStore(key);
    return {
      projetos: computed(() => store.state.projeto.projetos),
      store,
    };
  },
});
</script>
<style scoped>
.button {
  margin-left: 8px;
}
.box {
  background-color: var(--bg-primario);
  color: var(--texto-primario);
}
</style>
