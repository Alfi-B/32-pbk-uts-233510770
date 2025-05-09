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
          >
            <div class="kegiatan-content">
              <span class="kegiatan-text">{{ kegiatan.teks }}</span>
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
      
      this.simpanKegiatan();
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