<template>
  <div id="app">
    <nav>
      <div class="nav-wrapper blue darken-1">
        <a href="#" class="brand-logo center">Produtos</a>
      </div>
    </nav>

    <div class="container">
      <ul>
        <li v-for="(erro, index) of errors" :key="index">
          campo <b>{{ erro.field }}</b> - {{ erro.defaultMessage }}
        </li>
      </ul>

      <form @submit.prevent="salvar">
        <label>Nome</label>
        <input
          type="text"
          placeholder="Informe o nome do produto"
          v-model="produto.nome"
        />
        <label>Quantidade</label>
        <input
          type="number"
          placeholder="Informe a quantidade"
          v-model="produto.quantidade"
        />
        <label>Valor</label>
        <input
          type="text"
          placeholder="Informe o valor"
          v-model="produto.valor"
        />

        <button class="waves-effect waves-light btn-small">
          Salvar<i class="material-icons left">save</i>
        </button>
      </form>

      <table>
        <thead>
          <tr>
            <th>ID</th>
            <th>NOME</th>
            <th>QTD</th>
            <th>VALOR</th>
            <th>OPÇÕES</th>
          </tr>
        </thead>

        <tbody>
          <tr v-for="produto of produtos" :key="produto.id">
            <td>{{ produto.id }}</td>
            <td>{{ produto.nome }}</td>
            <td>{{ produto.quantidade }}</td>
            <td>{{ produto.valor }}</td>
            <td>
              <button
                @click="editar(produto)"
                class="waves-effect btn-small blue darken-1"
              >
                <i class="material-icons">create</i>
              </button>
              <button
                @click="remover(produto)"
                class="waves-effect btn-small red darken-1"
              >
                <i class="material-icons">delete_sweep</i>
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
import Produto from "./services/produtos";

export default {
  data() {
    return {
      //objeto produto
      produto: {
        id: "",
        nome: "",
        quantidade: "",
        valor: "",
      },

      produtos: [],

      errors: [],
    };
  },

  mounted() {
    //carregando a api
    this.listar(); //chamando o método listar que estar no methods
  },

  methods: {
    listar() {
      Produto.listar().then((resposta) => {
        console.log(resposta.data);
        this.produtos = resposta.data;
      });
    },

    salvar() {
      if (!this.produto.id) {
        Produto.salvar(this.produto)
          .then((resposta) => {
            this.produto = {}; //limpa os campos do formulário
            alert("Salvo com sucesso!"); // aparece uma mensagem de sucesso
            this.listar(); //atualiza na tabela o novo objeto salvo
            this.errors = []; //limpa as mensagens de erro
          })
          .catch((e) => {
            console.log(e.response.data.errors);
            this.errors = e.response.data.errors;
          });
      } else {
        Produto.atualizar(this.produto)
          .then((resposta) => {
            this.produto = {}; //limpa os campos do formulário
            alert("Atualizado com sucesso!"); // aparece uma mensagem de sucesso
            this.listar(); //atualiza na tabela o novo objeto salvo
            this.errors = []; //limpa as mensagens de erro
          })
          .catch((e) => {
            console.log(e.response.data.errors);
            this.errors = e.response.data.errors;
          });
      }
    },

    editar(produto) {
      this.produto = produto;
    },

    remover(produto) {
      if (
        confirm(
          "Tem certeza que deseja excluir o produto " +
            produto.nome +
            " selecionado?"
        )
      ) {
        Produto.excluir(produto)
          .then((resposta) => {
            this.listar();
            this.errors = []; //apaga os erros
          })
          .catch((e) => {
            console.log(e.response.data.errors);
            this.errors = e.response.data.errors;
          });
      }
    },
  },
};
</script>

<style>
</style>
