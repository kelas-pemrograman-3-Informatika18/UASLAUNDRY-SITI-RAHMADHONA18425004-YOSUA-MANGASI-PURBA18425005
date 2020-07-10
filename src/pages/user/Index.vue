<template>
  <q-page padding>
    <div class="q-mb-xl">
    <q-carousel

      animated
      v-model="slide"
      push
      arrows
      navigation
      vertical
      autoplay
      infinite
      swipeable
    >
      <q-carousel-slide :name="1" img-src="https://s3-ap-southeast-1.amazonaws.com/magazine.job-like.com/magazine/wp-content/uploads/2017/09/30090556/228.jpg" />
      <q-carousel-slide :name="2" img-src="https://encrypted-tbn0.gstatic.com/images?q=tbn%3AANd9GcQCJWoB8as1scEC1xtWhMgafOqDBIKSSZlcVQ&usqp=CAU" />
      <q-carousel-slide :name="3" img-src="https://encrypted-tbn0.gstatic.com/images?q=tbn%3AANd9GcTtoHXVEnTKoQNmYs35GBRGcAVnyW7-PgKxGQ&usqp=CAU" />
      <q-carousel-slide :name="4" img-src="https://encrypted-tbn0.gstatic.com/images?q=tbn%3AANd9GcRBNhcFNqDzOUaj2iQXK4gHstccmFuQT0FwXA&usqp=CAU" />
      <q-carousel-slide :name="5" img-src="https://encrypted-tbn0.gstatic.com/images?q=tbn%3AANd9GcTpLOdeJqzq6GyVey3eduwD7fnmc9Q5bDx91g&usqp=CAU" />
    </q-carousel>
    </div>
    <div class="row q-col-gutter-md">
    <div class="col-md-3 col-xs-12" v-for="(laundry, i) in data" :key="i">
    <q-card>
      <q-img
        :src="`${$baseImageURL}/${movie.image}`"
        :ratio="16/9"
      />

      <q-card-section>
        <q-btn
          fab
          color="deep-orange-10"
          icon="add_shopping_cart"
          class="absolute"
          unelevated
          style="top: 0; right: 12px; transform: translateY(-50%);"
        />

        <div class="row no-wrap items-center">
          <div class="col text-h6 ellipsis">
            {{laundry.jenispakaian}}
          </div>
        </div>

        <q-rating v-model="laundry.rating" readonly color="deep-indigo-5" :max="5" size="32px" />
      </q-card-section>

      <q-card-section class="q-pt-none">
        <div class="text-black-2 text-caption">
          Rp. {{ laundry.harga }},-
        </div>
        <div class="text-subtitle1">
          {{laundry.pilihanlaundry}}
        </div>
        <div @click="laundry.expanded = !laundry.expanded" class="text-caption text-deep-indigo-5 ellipsis-3-lines cursor-pointer">
          Tampilkan Deskripsi
        </div>
          <q-slide-transition>
            <div v-show="laundry.expanded">
              <div class="text-deep-indigo-5 text-caption">
                {{ laundry.deskripsi }}
              </div>
            </div>
          </q-slide-transition>
          </q-card-section>

      <q-card-actions>
        <q-btn unelevated @click="openDetail(laundry)" class="full-width" color="primary">
          Order Now or Cry Later
        </q-btn>
      </q-card-actions>
    </q-card>
    </div>
    </div>
    <q-dialog v-model="dialog" v-if="dialog" position="bottom">
      <q-card style="width: 500px">
        <q-card-section>
          <div class="text-h6">Detail Pemesanan</div>
         </q-card-section>
         <q-card-section style="max-height: 50vh;" class="scrool">
         {{ activeData.jenispakaian }} Rp. {{ activeData.harga }},-
         <q-form class="q-mt-md">
          <q-input filled type="number" class="q-mb-md" v-model="jumlah" label="Masukkan Jumlah"/>
          Rp. {{ total }},-
             <q-file color="light-green-8" class="q-mt-md" filled v-model="image" label="Label">
                 <template v-slot:prepend>
                  <q-icon name="cloud_upload" />
                 </template>
             </q-file>
         </q-form>
         </q-card-section>
         <q-card-actions align="right">
          <q-btn flat label="Batal" v-close-popup/>
          <q-btn color="deep-orange-10" @click="prosesTransaksi()" label="Proses"/>
         </q-card-actions>
       </q-card>
    </q-dialog>
  </q-page>
</template>
<script>
export default {
  data () {
    return {
      slide: 1,
      stars: 4,
      dialog: false,
      image: null,
      data: [],
      activeData: null,
      jumlah: 1
    }
  },
  created () {
    this.getData()
  },
  methods: {
    getData () {
      this.$axios.get('laundry/getall')
        .then(res => {
          if (res.data.sukses) {
            this.data = res.data.data.map(movie => {
              return Object.assign(movie, {
                expanded: false
              })
            })
          } else {
            this.$showNotif(res.data.pesan, 'negative')
          }
        })
    },
    openDetail (data) {
      this.activeData = data
      this.dialog = true
    },
    prosesTransaksi () {
      const formData = new FormData()
      formData.append('image', this.image)
      formData.append('data', JSON.stringify({
        idUser: this.$q.localStorage.getItem('dataUser')._id,
        idFilm: this.activeData._id,
        harga: this.activeData.harga,
        jumlah: this.jumlah,
        total: this.total
      }))
      this.$axios.post('order/insert', formData)
        .then(res => {
          if (res.data.sukses) {
            this.$showNotif(res.data.pesan, 'positive')
            this.dialog = false
            this.image = null
          } else {
            this.$showNotif(res.data.pesan, 'negative')
          }
        })
    }
  },
  computed: {
    total () {
      return this.activeData.harga * this.jumlah
    }
  }
}
</script>
