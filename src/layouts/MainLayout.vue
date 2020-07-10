<template>
  <q-layout view="lHh Lpr lFf">
    <q-header class=" bg-white text-deep-orange-10">
      <q-toolbar>
        <q-btn dense flat round @click="leftDrawerOpen = !leftDrawerOpen" icon="menu" />
        <q-space></q-space>
         <q-btn round>
            <q-avatar>
              <img src="https://cdn.quasar.dev/img/avatar.png">
    </q-avatar>
    <q-menu>
          <q-list style="min-width: 100px">
            <q-separator />
            <q-item clickable v-close-popup>
              <q-item-section>Profile</q-item-section>
            </q-item>
            <q-separator />
            <q-item clickable v-close-popup @click="logout()">
              <q-item-section>Logout</q-item-section>
            </q-item>
          </q-list>
        </q-menu>
         </q-btn>
      </q-toolbar>
    </q-header>

    <q-drawer
      v-model="leftDrawerOpen"
      show-if-above
      content-class="text-white bg-primary"
    >
       <q-scroll-area class="fit">
        <q-list>
          <q-item class="justify-center text-center q-mb-md q-mt-md">
           <div>
             <q-avatar size="120px">
                <img src="https://cdn.quasar.dev/img/avatar.png">
             </q-avatar>
             <div class="text-weight-bold q-mt-md">{{ $q.localStorage.getItem('dataUser').namaLengkap }}</div>
             <div>Aplikasi FiqYol Clothing Shop</div>
           </div>
         </q-item>

      <q-item clickable active-class="active" v-ripple exact :to="{ name: 'dashboardAdmin' }">
        <q-item-section avatar>
          <q-icon name="dashboard"/>
        </q-item-section>
        <q-item-section>
          <q-item-label>Dashboard</q-item-label>
        </q-item-section>
      </q-item>
      <q-item clickable active-class="active" v-ripple exact :to="{ name: 'jenispakaian' }">
        <q-item-section avatar>
          <q-icon name="dashboard"/>
        </q-item-section>
        <q-item-section>
          <q-item-label>Data Baju</q-item-label>
        </q-item-section>
      </q-item>
      <q-item clickable active-class="active" :to="{ name: 'inputlaundry' }" v-ripple exact>
        <q-item-section avatar>
          <q-icon name="input"/>
        </q-item-section>
        <q-item-section>
          <q-item-label>Input laundry</q-item-label>
        </q-item-section>
      </q-item>
      <q-item clickable active-class="active" :to="{ name: 'dataUser' }" v-ripple exact>
        <q-item-section avatar>
          <q-icon name="supervisor_account"/>
        </q-item-section>
        <q-item-section>
          <q-item-label>Data User</q-item-label>
        </q-item-section>
      </q-item>

    </q-list>
       </q-scroll-area>
    </q-drawer>

    <q-page-container class="bg-deep-indigo-5">
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
export default {
  name: 'MainLayout',

  data () {
    return {
      leftDrawerOpen: false
    }
  },
  methods: {
    logout () {
      this.$q.localStorage.remove()
      this.$router.push({ name: 'loginPage' })
    }
  }
}
</script>
<style scoped>
.active {
  color: #000000;
  background: #fafafa;
  padding-right: 10px;
}
</style>
