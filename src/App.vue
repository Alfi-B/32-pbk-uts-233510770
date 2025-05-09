<template>
  <div class="app-container">
    <h1>Paket Yang Perlu Diantar</h1>
    
    <div class="main-container">
      <div class="form-section">
        <form @submit.prevent="tambahKegiatan" class="add-form">
          <input 
            v-model="inputKegiatan" 
            placeholder="Masukkan kegiatan baru..." 
            required
            class="kegiatan-input"
          />
          <button type="submit" class="btn-add">Tambah</button>
        </form>
      </div>

      <div class="activity-list">
        <h2>Daftar Paket</h2>
        <div v-if="daftarKegiatan.length === 0" class="empty-state">
          <p>Belum ada kegiatan. Silakan tambahkan kegiatan baru!</p>
        </div>
        <ul class="kegiatan-list">
          <li 
            v-for="(kegiatan, index) in daftarKegiatan" 
            :key="kegiatan.id"
            class="kegiatan-item"
            :class="{ 'animated': animasiIndex === index }"
          >
            <div class="kegiatan-content">
              <span class="kegiatan-text">{{ kegiatan.teks }}</span>
            </div>
            <div class="kegiatan-actions">
              <button 
                @click="batalkanKegiatan(kegiatan.id)" 
                class="btn-delete"
                title="Hapus Kegiatan"
              >
                <span>✕</span>
              </button>
            </div>
          </li>
        </ul>
      </div>
    </div>

    <footer class="footer">
      <p>To Do List Buatan Alfi Ardiansyah</p>
    </footer>
  </div>
</template>

<script>
export default {
  data() {
    return {
      daftarKegiatan: [],
      inputKegiatan: '',
      animasiIndex: -1,
      lastId: 0
    }
  },
  methods: {
    tambahKegiatan() {
      if (this.inputKegiatan.trim() === '') return;
      
      const newId = this.lastId + 1;
      this.lastId = newId;
      
      const newKegiatan = {
        id: newId,
        teks: this.inputKegiatan,
        selesai: false,
        timestamp: new Date()
      };
      
      this.daftarKegiatan.unshift(newKegiatan);
      this.inputKegiatan = '';
      
      this.animasiIndex = 0;
      setTimeout(() => {
        this.animasiIndex = -1;
      }, 500);
      
      this.simpanKegiatan();
    },
    
    batalkanKegiatan(id) {
      const index = this.daftarKegiatan.findIndex(kegiatan => kegiatan.id === id);
      if (index !== -1) {
        this.animasiIndex = index;
        setTimeout(() => {
          this.daftarKegiatan.splice(index, 1);
          this.simpanKegiatan();
          this.animasiIndex = -1;
        }, 300);
      }
    },
    
    simpanKegiatan() {
      localStorage.setItem('daftarKegiatan', JSON.stringify(this.daftarKegiatan));
      localStorage.setItem('lastId', this.lastId.toString());
    },
    
    muatKegiatan() {
      const savedKegiatan = localStorage.getItem('daftarKegiatan');
      const savedId = localStorage.getItem('lastId');
      
      if (savedKegiatan) {
        this.daftarKegiatan = JSON.parse(savedKegiatan);
      }
      
      if (savedId) {
        this.lastId = parseInt(savedId);
      }
    }
  },
  mounted() {
    this.muatKegiatan();
  }
}
</script>