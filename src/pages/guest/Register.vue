<template>
    <q-layout>
        <q-page-container>
            <q-page padding class="bg-indigo-5">
                <div class="row">
                    <q-card class="fixed-center col-md-4 col-xs-12">
                        <q-card-section>
                            <div class="text-h5">
                                Registrasi yukk
                            </div>
                            <q-form @submit="onSubmit">
                                <q-input
                                v-model="username"
                                label="Username"
                                :rules="[
                                val => val && val.length > 0 || 'Masukkan Username'
                                ]"
                                />

                                <q-input
                                v-model="namaLengkap"
                                label="Nama Lengkap"
                                :rules="[
                                val => val && val.length > 0 || 'Masukkan Nama Lengkap'
                                ]"/>

                                <q-input
                                v-model="email"
                                label="E-mail"
                                :rules="[
                                val => val && val.length > 0 || 'Masukkan E-mail'
                                ]"/>

                                <q-input
                                v-model="password"
                                label="Password"
                                type="password"
                                :rules="[
                                val => val && val.length > 0 || 'Masukkan Password'
                                ]"
                                />
                                <q-card-section>
                                    <div class="q-gutter-md">
                                        <q-btn label="Registrasi" unelevated type="submit" color="indigo" class="full-width"/>
                                        <q-btn :to="{ name: 'loginPage' }" label="Login" type="submit" class="full-width q-mb-md q-mt-md"/>
                                    </div>
                                </q-card-section>
                            </q-form>
                        </q-card-section>
                    </q-card>
                </div>
            </q-page>
        </q-page-container>
    </q-layout>
</template>
<script>
export default {
  data () {
    return {
      username: null,
      namaLengkap: null,
      email: null,
      password: null
    }
  },
  methods: {
    onSubmit () {
      this.$axios.post('user/register', {
        username: this.username,
        namaLengkap: this.namaLengkap,
        email: this.email,
        password: this.password
      }).then(res => {
        if (res.data.sukses) {
          this.$showNotif(res.data.pesan, 'positive')
          this.$router.push({ name: 'loginPage' })
        } else {
          this.$showNotif(res.data.pesan, 'negative')
        }
      })
    }
  }
}
</script>
