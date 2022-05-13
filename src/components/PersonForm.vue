<template>
  <div>
    <Message :msg="msg" v-show="msg" />
    <div>
      <form id="person-form" @submit="registerPerson">
        <div class="input-container">
          <label for="tipo">Tipo (Pessoa Física/Pessoa Jurídica):*</label>
          <select
            name="tipo"
            id="tipo"
            v-model="tipo"
            required
            style="padding: 5px 10px; height: 50px; background-color: #eeeeee"
          >
            <option v-for="tipo in tipos" :key="tipo.id" :value="tipo.dado">
              {{ tipo.dado }}
            </option>
          </select>
        </div>
        <div class="input-container">
          <label for="nome">Nome:*</label>
          <v-text-field
            v-if="!idEdit"
            type="text"
            name="nome"
            id="nome"
            v-model="nome"
            :rules="nameRules"
            placeholder="Digite seu Nome"
            required
          />
          <v-text-field
            v-else
            type="text"
            name="nome"
            id="nome"
            v-model="nome"
            :rules="nameRules"
            placeholder="Digite seu Nome"
            required
          />
        </div>
        <div class="input-container" v-if="tipo == 'Pessoa Jurídica'">
          <label for="identificacao">CNPJ:*</label>
          <v-text-field
            type="text"
            name="identificacao"
            id="identificacao"
            v-model="identificacao"
            :rules="cnpjRules"
            placeholder="Digite seu CNPJ"
            required
          />
        </div>
        <div class="input-container" v-else>
          <label for="identificacao">CPF:*</label>
          <v-text-field
            type="text"
            name="identificacao"
            id="identificacao"
            v-model="identificacao"
            :rules="cpfRules"
            placeholder="Digite seu CPF"
            
            required
          />
        </div>
        <div class="input-container" style="flex-direction: row">
          <label v-if="!foto" for="foto" id="label-file">Anexar Foto</label>
          <label v-else for="foto" id="label-file">Alterar Foto</label>
          <label v-if="foto" id="label-file" @click="removePhoto()">
            Remover Foto
          </label>
          <input
            type="file"
            id="foto"
            name="foto"
            accept="image/jpg, image/png, image/jpeg"
            @change="uploadImage()"
          />
        </div>
        <div v-if="foto" class="input-container">
          <img :src="foto" />
        </div>
        <div class="input_container">
          <input type="submit" class="submit-btn" value="CONFIRMAR" />
        </div>
      </form>
    </div>
  </div>
</template>

<script>
import Message from "./Message.vue";
//import { mapMutations, mapState, mapGetters, mapActions } from "vuex";

export default {
  name: "PersonForm",
  data() {
    return {
      tipos: null,
      tipo: null,
      nome: null,
      identificacao: null,
      foto: null,
      msg: null,
      idEdit: null,
      nameRules: [
        (value) => !!value || "O nome é obrigatório",
        (value) =>
          (value && value.length >= 3) ||
          "O nome deve ter no mínimo 3 caracteres",
        (value) =>
          (value && value.length <= 10) ||
          "O nome deve ter menos do que 10 caracteres",
      ],
      cpfRules: [
        (value) => !!value || "O CPF é obrigatório",
        (value) => /.\..+\..+-+./.test(value) || "O CPF não é válido",
      ],
      cnpjRules: [
        
        (value) => !!value || "O CNPJ é obrigatório",
        (value) => /.\..+\..+\/.+-./.test(value) || "O CNPJ não é válido",
      ],
    };
  },
  methods: {
    //...mapMutations([]),
    //...mapActions([]),
    async getDatabase() {
      const req = await fetch("http://localhost:3000/database");
      const data = await req.json();
      this.tipos = data.tipos;
    },
    async registerPerson(e) {
      e.preventDefault();
      const data = {
        tipo: this.tipo,
        nome: this.nome,
        identificacao: this.identificacao,
        foto: this.foto,
      };
      const dataJson = JSON.stringify(data);
      if (this.idEdit) {
        const req = await fetch(
          `http://localhost:3000/peoplesbase/${this.idEdit}`,
          {
            method: "PATCH",
            headers: { "Content-Type": "application/json" },
            body: dataJson,
          }
        );
        const res = await req.json();
        //colocar uma mensagem de sistema
        this.msg = `Pessoa ${res.id} alterada com sucesso`;
      } else {
        const req = await fetch("http://localhost:3000/peoplesbase", {
          method: "POST",
          headers: { "Content-Type": "application/json" },
          body: dataJson,
        });
        const res = await req.json();
        //colocar uma mensagem de sistema
        this.msg = `Pessoa ${res.id} cadastrada com sucesso`;
      }
      //limpar mensagem
      setTimeout(() => (this.msg = ""), 3000);
      //limpar os campos
      this.tipo = "";
      this.nome = "";
      this.identificacao = "";
      this.foto = "";
      this.idEdit = "";
    },
    //imagem para base64
    uploadImage() {
      const file = document.querySelector("#foto").files[0];
      const reader = new FileReader();

      let rawImg;
      reader.onloadend = () => {
        rawImg = reader.result;
        this.foto = rawImg;
      };
      reader.readAsDataURL(file);
    },
    removePhoto() {
      this.foto = "";
    },
    async getCadastroId(id) {
      const req = await fetch(`http://localhost:3000/peoplesbase/${id}`);
      const data = await req.json();

      this.tipo = data.tipo;
      this.nome = data.nome;
      this.identificacao = data.identificacao;
      this.foto = data.foto;
    },
  },

  computed: {
    //...mapGetters([]),
    //...mapState(),
  },

  mounted() {
    this.getDatabase();
    let id = this.$route.params.id;
    if (id) {
      this.idEdit = id;
      this.getCadastroId(id);
    }
    id = null;
  },
  components: {
    Message,
  },
};
</script>

<style scoped>
#person-form {
  width: 350px;
  margin: 0 auto;
}

.input-container {
  display: flex;
  flex-direction: column;
  margin-bottom: 20px;
}

label {
  font-weight: bold;
  margin-bottom: 15px;
  color: #222;
  padding: 5px 10px;
  border-left: 4px solid #42b883;
}

input,
select {
  padding: 5px 10px;
  width: 350px;
  background-color: #fff;
  border-radius: 3px;
  border: 1px solid #222;
}

.submit-btn {
  background-color: #222;
  color: #ffffff;
  font-weight: bold;
  border: 2px solid #222;
  border-radius: 3px;
  padding: 10px;
  font-size: 14px;
  margin: 0 auto;
  cursor: pointer;
  transition: 0.5s;
}

.submit-btn:hover {
  background-color: #ffffff;
  color: #222;
}

#foto {
  display: none;
}

#label-file {
  background-color: #42b883;
  color: #ffffff;
  border-radius: 3px;
  font-size: 14px;
  cursor: pointer;
  margin: 0 auto;
  padding: 5px 10px;
}
</style>