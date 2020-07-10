<template>
  <q-page padding>
    <div class="row q-mb-md col-gutter-md">
      <div class="col-md-12 col-xs-12 col-lg-12">
        <div class="row">
          <div class="col-auto">
            <div class="left"></div>
          </div>
          <div class="col">
            <q-banner inline-actions class="text-deep-orange-10">
              <div class="text-h6">Data Transaksi</div>
              <div >Data Transaksi Pembelian Dan Pemesanan Anda</div>
            </q-banner>
          </div>
        </div>
      </div>
    </div>
    <q-card flat>
          <q-table
      :data="data"
      flat
      :columns="columns"
      row-key="name"
    >
        <template v-slot:body="props">
          <q-tr :props="props">
            <q-td key="jenispakaian" :props="props">
              {{ props.row.jenispakaian[0].jenispakaian }}
            </q-td>
            <q-td key="harga" :props="props">
              {{ props.row.harga }}
            </q-td>
            <q-td key="jumlah" :props="props">
              {{ props.row.jumlah }}
            </q-td>
            <q-td key="total" :props="props">
              {{ props.row.total }}
            </q-td>
            <q-td key="status" :props="props">
              <q-badge v-if="props.row.status === 1" weight="1kg" class="q-pa-sm">
                Belum di Konfirmasi
              </q-badge>
              <q-badge v-if="props.row.status === 2" weight="2kg" class="q-pa-sm">
                Sedang Dalam Proses
              </q-badge>
              <q-badge v-if="props.row.status === 3" weight="3kg" class="q-pa-sm">
                Sudah Selesai
              </q-badge>
            </q-td>
            <q-td key="aksi" :props="props">
              <q-btn :disable="props.row.status !== 2" label="Terima" @click="confirm(props.row._id)" class="q-ml-sm" weight="3kg" unelevated/>
            </q-td>
          </q-tr>
        </template>
      </q-table>
    </q-card>
  </q-page>
</template>

<script>
export default {
  name: 'PageIndex',
  data () {
    return {
      columns: [
        { name: 'jenispakaian', align: 'center', label: 'Jenis Pakaian', field: 'jenispakaian', sortable: true },
        { name: 'harga', align: 'center', label: 'Harga', field: 'harga', sortable: true },
        { name: 'jumlah', align: 'center', label: 'Jumlah Beli', field: 'jumlah', sortable: true },
        { name: 'total', align: 'center', label: 'Total ', field: 'total', sortable: true },
        { name: 'status', align: 'center', label: 'Status', field: 'status', sortable: true },
        { name: 'aksi', align: 'center', label: 'Aksi', field: 'aksi' }
      ],
      data: [],
      detail: false,
      activeData: null
    }
  },
  created () {
    this.getData()
  },
  methods: {
    getData () {
      this.$axios.get(`order/getallorderbyuser/${this.$q.localStorage.getItem('dataUser')._id}`)
        .then((res) => {
          if (res.data.sukses) {
            this.data = res.data.data
          }
        })
    },
    confirm (id) {
      this.$q.dialog({
        title: 'Konfirmasi',
        message: 'Terima Barang?',
        cancel: true,
        persistent: true
      }).onOk(() => {
        this.$axios.put(`order/termiabarang/${id}`)
          .then((res) => {
            if (res.data.sukses) {
              this.$showNotif(res.data.pesan, 'positive')
              this.getData()
            } else {
              this.$showNotif(res.data.pesan, 'negative')
            }
          })
      })
    }
  }
}
</script>
<style scoped>
.left {
  width: 4px;
  height: 100%;
  background-color: rgb(185, 2, 2);
}
</style>
