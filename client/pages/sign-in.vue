<template>
  <div>
    <form>
      <h2>Sign in!</h2>
      <ul>
        <li><input v-model="email" type="text" placeholder="E-mail"></li>
        <li><input v-model="password" type="password" placeholder="Password"></li>
        <li><input type="submit" @click.prevent="signIn"></li>
      </ul>
      <small v-if="error">{{ error }}</small>
    </form>

    <form>
      <h2>Register!</h2>
      <ul>
        <li><input v-model="email" type="text" placeholder="E-mail"></li>
        <li><input v-model="password" type="password" placeholder="Password"></li>
        <li><input type="submit" @click.prevent="register"></li>
      </ul>
    </form>
  </div>
</template>

<script>
import { mapActions } from 'vuex'; // eslint-disable-line import/no-extraneous-dependencies

export default {
  name: 'authentication',

  data() {
    return {
      email: null,
      password: null,
      error: null,
    };
  },

  methods: {
    ...mapActions('auth', {
      authenticate: 'authenticate',
    }),

    ...mapActions('users', {
      createUser: 'create',
    }),

    async signIn() {
      const email = this.email;
      const password = this.password;

      try {
        const authenticated = await this.authenticate({ strategy: 'local', email, password });

        if (authenticated) {
          this.error = null;
          this.$router.push('/admin');
        }
      } catch (error) {
        switch (error.message) {
          case 'Error': {
            this.error = 'Wrong credentials!';
            break;
          }
          default: {
            this.error = error.message;
          }
        }

        throw error;
      }
    },

    async register() {
      const email = this.email;
      const password = this.password;

      try {
        await this.createUser({ email, password });
      } catch (error) {
        throw error;
      }
    },
  },
};
</script>

<style scoped>
h2 {
  margin: 0;
}

div {
  display: flex;
  justify-content: center;
}

form {
  margin: 1em;
  padding: 1em;
  border: 1px solid #DDD;

}

small {
  color: red;
}
</style>
