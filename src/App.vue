<template>
  <div class="app-container">
    <div class="header-container">
      <h1>Paket Yang Perlu Diantar</h1>
      <div class="theme-switch" @click="toggleTheme">
        <span v-if="darkMode">☀️</span>
        <span v-else>🌙</span>
      </div>
    </div>
    
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

      <div class="filter-section">
        <div class="filter-label"></div>
        <div class="filter-buttons">
          <button 
            @click="showBelumSelesai = false" 
            :class="['btn-filter', { active: !showBelumSelesai }]"
          >
            Semua
          </button>
          <button 
            @click="showBelumSelesai = true" 
            :class="['btn-filter', { active: showBelumSelesai }]"
          >
            Belum Selesai
          </button>
        </div>
      </div>

      <div class="activity-list">
        <h2>Daftar Paket</h2>
        <div v-if="kegiatanTampil.length === 0" class="empty-state">
          <p>Belum ada kegiatan. Silakan tambahkan kegiatan baru!</p>
        </div>
        <TransitionGroup name="list" tag="ul" class="kegiatan-list">
          <li 
            v-for="(kegiatan, index) in kegiatanTampil" 
            :key="kegiatan.id"
            class="kegiatan-item"
            :class="{ 'animated': animasiIndex === index }"
          >
            <div class="kegiatan-content">
              <label class="checkbox-container">
                <input 
                  type="checkbox" 
                  v-model="kegiatan.selesai"
                  @change="updateKegiatan(kegiatan)"
                />
                <span class="checkmark"></span>
              </label>
              <span 
                :class="['kegiatan-text', { 'selesai': kegiatan.selesai }]"
              >{{ kegiatan.teks }}</span>
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
        </TransitionGroup>
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
      showBelumSelesai: false,
      animasiIndex: -1,
      lastId: 0,
      darkMode: false
    }
  },
  computed: {
    kegiatanTampil() {
      if (this.showBelumSelesai) {
        return this.daftarKegiatan.filter(kegiatan => !kegiatan.selesai)
      } else {
        return this.daftarKegiatan
      }
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
    
    updateKegiatan(kegiatan) {
      this.simpanKegiatan();
    },
    
    toggleFilter() {
      this.showBelumSelesai = !this.showBelumSelesai;
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
    },
    
    toggleTheme() {
      this.darkMode = !this.darkMode;
      document.body.classList.toggle('dark-mode', this.darkMode);
      localStorage.setItem('darkMode', this.darkMode.toString());
    },
    
    checkTheme() {
      const savedTheme = localStorage.getItem('darkMode');
      if (savedTheme === 'true') {
        this.darkMode = true;
        document.body.classList.add('dark-mode');
      }
    }
  },
  mounted() {
    this.muatKegiatan();
    this.checkTheme();
  }
}
</script>
