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
      <q-carousel-slide :name="1" img-src="https://cdn.idntimes.com/content-images/community/2019/04/10-pilihan-dress-artis-muda-indonesia-sukses-tampil-feminim-2fe077b27e1c8ad69fb535351bc06c2c_600x400.jpg" />
      <q-carousel-slide :name="2" img-src="https://1.bp.blogspot.com/-JfCwv7HII0M/WTarHbMZRkI/AAAAAAAAAS0/L50IMi5eleQW-cgDVtZKWNw8XlUm4ttLQCLcB/s1600/1156102_8127307a-fb6c-464e-bfd7-f887e88ae6c9.jpg" />
      <q-carousel-slide :name="3" img-src="https://cdn.shopify.com/s/files/1/0714/1079/products/chase-hudson-merch-chase-hudson-huddy-jellyfish-black-hoodie-hoodie-14578372247661_2048x.jpg?v=1586495942" />
      <q-carousel-slide :name="4" img-src="https://cdn2.tstatic.net/tribunnews/foto/bank/images/nia-ramadhani-kenakan-baju-sama-dengan-4-artis-berikut-ini.jpg" />
      <q-carousel-slide :name="5" img-src="https://cdn2.tstatic.net/tribunnews/foto/bank/images/blackpink-442019.jpg" />
    </q-carousel>
    </div>
    <div class="row q-col-gutter-md">
    <div class="col-md-3 col-xs-12" v-for="(baju, i) in data" :key="i">
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
            {{baju.namaBaju}}
          </div>
        </div>

        <q-rating v-model="baju.rating" readonly color="deep-orange-10" :max="5" size="32px" />
      </q-card-section>

      <q-card-section class="q-pt-none">
        <div class="text-black-2 text-caption">
          Rp. {{ baju.harga }},-
        </div>
        <div class="text-subtitle1">
          {{baju.ukuran}}
        </div>
        <div @click="baju.expanded = !baju.expanded" class="text-caption text-deep-orange-10 ellipsis-3-lines cursor-pointer">
          Tampilkan Deskripsi
        </div>
          <q-slide-transition>
            <div v-show="baju.expanded">
              <div class="text-deep-orange-10 text-caption">
                {{ baju.deskripsi }}
              </div>
            </div>
          </q-slide-transition>
          </q-card-section>

      <q-card-actions>
        <q-btn unelevated @click="openDetail(baju)" class="full-width" color="primary">
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
         {{ activeData.namaBaju }} Rp. {{ activeData.harga }},-
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
      this.$axios.get('baju/getall')
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
