<template>
  <div class="box formulario">
    <div class="columns">
      <div
        class="column is-5"
        role="form"
        aria-label="Formulario para ciração de uma nova tarefa"
      >
        <input
          type="text"
          class="input"
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
              :key="projeto.id">
              {{projeto.nome}}
            </option>
          </select>
        </div>
      </div>
      <div class="column">
        <Temporizador @ao-temporizador-finalizado="finalizarTarefa"/>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { computed, defineComponent } from "vue";
import { useStore } from "vuex";
import Temporizador from "./Temporizador.vue";
import { key } from "@/store";
import { NOTIFICAR } from "@/store/tipo-mutacoes";
import { TipoNotificacao } from "@/interfaces/INotificacao";

export default defineComponent({
    // eslint-disable-next-line vue/multi-word-component-names
    name: "Formulario",
    emits: ['aoSalvarTarefa'],
    components: {
      Temporizador
    },
    data () {
      return {
        descricao: '',
        idProjeto: ''
      }
    },
    methods: {
      finalizarTarefa (tempoDecorrido: number) : void {
        const projeto = this.projetos.find((p) => p.id == this.idProjeto);
        if (!projeto){
          this.store.commit(NOTIFICAR, {
            titulo: 'Ops!',
            texto: 'Selecione um projeto antes de finalizar a tarefa!',
            tipo: TipoNotificacao.FALHA,
          });
          return;
        }
        this.$emit('aoSalvarTarefa', {
          duracaoEmSegundos: tempoDecorrido,
          descricao: this.descricao,
          projeto: this.projetos.find(proj => proj.id == this.idProjeto)
        })
        this.descricao = ''
      }
    },
    setup () {
      const store = useStore(key)
      return {
        store,
        projetos: computed(() => store.state.projetos)
      }
    }
});
</script>

<style>
.formulario {
  color: var(--texto-primario);
  background-color: var(--bg-primario);
}
</style>