<template>
  <b-card class="card-cadastro shadow-sm px-4 py-4">
    <h5 class="text-dark-pink text-center text-bold mb-1">
      Preencha o campo abaixo
    </h5>
    <p class="text-dark-gray text-center">É rápido, simples e seguro</p>
    <ValidationObserver ref="observer" v-slot="{ invalid }">
      <b-form>
        <ValidationProvider
          rules="required|email"
          v-slot="{ errors }"
          class="input-validation"
        >
          <label for="email" class="text-dark-gray mb-2 w-100"
            >Email
            <b-input
              class="px-3 py-4 input-border"
              id="email"
              type="email"
              v-model="email"
            />
            <span class="error"
              >{{ errors[0] }}{{ errorEmail ? "Email já existe" : "" }}</span
            >
          </label>
        </ValidationProvider>
        <ValidationProvider
          v-if="index > 0"
          rules="required"
          v-slot="{ errors }"
          class="input-validation"
        >
          <label for="nome" class="text-dark-gray mb-2 w-100"
            >Nome
            <b-input
              class="px-3 py-4 input-border"
              id="nome"
              type="text"
              v-model="nome"
            />
            <span class="error">{{ errors[0] }}</span>
          </label>
        </ValidationProvider>
        <ValidationProvider
          v-if="index > 0"
          rules="required|min:8"
          vid="senha"
          v-slot="{ errors }"
        >
          <label for="senha" class="text-dark-gray mb-2 w-100"
            >Senha
            <b-input
              class="px-3 py-4 input-border"
              id="senha"
              type="password"
              v-model="senha"
            />
            <span class="error">{{ errors[0] }}</span>
          </label>
        </ValidationProvider>

        <ValidationProvider
          v-if="index > 0"
          rules="required|confirmed:senha"
          v-slot="{ errors }"
        >
          <label for="confirmarSenha" class="text-dark-gray mb-2 w-100"
            >Confirmar senha
            <b-input
              class="px-3 py-4 input-border"
              id="confirmarSenha"
              v-model="confirmarSenha"
              type="password"
            />
            <span class="error">{{ errors[0] }}</span>
          </label>
        </ValidationProvider>
        <c-button
          :disabled="invalid"
          color="secondary"
          class="btn-entrar text-center w-100 py-3 mt-4 mb-3"
          @click="continuar"
          >Continuar</c-button
        >
      </b-form>
    </ValidationObserver>
  </b-card>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import { ValidationProvider, ValidationObserver, extend } from "vee-validate";
import { required, email, min, confirmed } from "vee-validate/dist/rules";
import CButton from "@/components/custom-button/CustomButton.vue";
import {} from "vee-validate";

extend("confirmed", {
  ...confirmed,
  message: "As senhas não são iguais",
});

extend("min", {
  ...min,
  message: "A senha deve ter no mínimo 8 dígitos",
});

extend("email", {
  ...email,
  message: "Email inválido",
});

extend("required", {
  ...required,
  message: "Campo obrigatório",
});

@Component({
  name: "CardCadastro",
  components: {
    ValidationProvider,
    ValidationObserver,
    CButton,
  },
})
export default class CardCadastro extends Vue {
  email = "";
  nome = "";
  senha = "";
  confirmarSenha = "";
  index = 0;
  errorEmail = false;
  criouConta = false;

  async cadastrar(): Promise<void> {
    const observerRef = this.$refs.observer;

    const validForm = await (
      observerRef as InstanceType<typeof ValidationProvider>
    ).validate();

    if (this.index === 1 && validForm) {
      this.errorEmail = false;
      await this.$store
        .dispatch("cadastro", {
          email: this.email,
          senha: this.senha,
          nome: this.nome,
        })
        .then(() => {
          alert("Conta criada com sucesso!");
          this.$router.push("/agendamento");
        })
        .catch((e) => {
          this.errorEmail = true;
        });
    }
  }
  continuar(): void {
    if (this.index === 0) {
      this.index++;
      this.$emit("confirmouEmail");
    } else {
      this.cadastrar();
    }
  }
}
</script>

<style>
.btn-fechar-confirmacao {
  border: 1px solid var(--light-gray);
  background-color: white !important;
  border-color: #ababab !important;
  color: var(--dark-gray) !important;
}
.card-cadastro {
  width: 100%;
}

.error {
  font-size: 0.9rem;
  color: var(--red);
}
.text-bold {
  font-weight: 700;
}

.text-dark-gray {
  color: var(--dark-gray);
}

.text-dark-pink {
  color: var(--dark-pink);
}
.input-border {
  border: 1px solid var(--light-gray);
}
.btn-entrar {
  font-weight: 700;
  color: white;
}
@media only screen and (max-width: 1024px) {
  .card-cadastro {
    padding: 5px !important;
  }
}
</style>
