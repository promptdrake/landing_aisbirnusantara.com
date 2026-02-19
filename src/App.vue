<script setup>
import { onBeforeUnmount, onMounted, ref } from 'vue'

const activeSection = ref('why')
const isDark = ref(false)
const heroImageRef = ref(null)
const imageTransform = ref({ rotateX: 0, rotateY: 0 })
let observer

onMounted(() => {
  const sections = Array.from(document.querySelectorAll('section[id]'))

  // Check for saved theme preference
  const savedTheme = localStorage.getItem('theme')
  if (savedTheme === 'dark') {
    isDark.value = true
    document.documentElement.classList.add('dark')
  }

  observer = new IntersectionObserver(
    (entries) => {
      entries.forEach((entry) => {
        if (entry.isIntersecting) {
          activeSection.value = entry.target.id
        }
      })
    },
    {
      root: null,
      threshold: 0.3,
      rootMargin: '-10% 0px -60% 0px',
    }
  )

  sections.forEach((section) => observer.observe(section))

  // 3D effect for hero image on desktop
  const handleMouseMove = (e) => {
    if (window.innerWidth < 900 || !heroImageRef.value) return
    
    const rect = heroImageRef.value.getBoundingClientRect()
    const x = e.clientX - rect.left
    const y = e.clientY - rect.top
    
    const centerX = rect.width / 2
    const centerY = rect.height / 2
    
    const rotateX = ((y - centerY) / centerY) * -10
    const rotateY = ((x - centerX) / centerX) * 10
    
    imageTransform.value = { rotateX, rotateY }
  }

  const handleMouseLeave = () => {
    imageTransform.value = { rotateX: 0, rotateY: 0 }
  }

  if (window.innerWidth >= 900) {
    document.addEventListener('mousemove', handleMouseMove)
    if (heroImageRef.value) {
      heroImageRef.value.addEventListener('mouseleave', handleMouseLeave)
    }
  }
})

onBeforeUnmount(() => {
  if (observer) {
    observer.disconnect()
  }
})

const toggleTheme = () => {
  isDark.value = !isDark.value
  if (isDark.value) {
    document.documentElement.classList.add('dark')
    localStorage.setItem('theme', 'dark')
  } else {
    document.documentElement.classList.remove('dark')
    localStorage.setItem('theme', 'light')
  }
}

const navigateToDash = () => {
  window.location.href = 'https://dash.aisbirnusantara.com'
}
</script>

<template>
  <div class="page">
    <header class="nav glass reveal" style="background: rgba(0, 188, 212, 0.1); border: 1px solid rgba(0, 188, 212, 0.2);">
      <div class="brand">
      <img src="https://assets.aisbirnusantara.com/20260217_121832.png" alt="Aisbir Cloud Nusantara" class="brand-logo reveal" style="width: 32px; height: 32px;" />
      <div>
        <p class="brand-name">Aisbir Cloud Nusantara</p>
        <p class="brand-tag">Cloud hosting provider</p>
      </div>
      </div>
      <input id="nav-toggle" class="nav-toggle" type="checkbox" />
      <label class="nav-toggle-label" for="nav-toggle">
      <span></span>
      <span></span>
      <span></span>
      </label>
      <div class="nav-menu">
      <nav class="nav-links">
        <a href="#why" :class="{ active: activeSection === 'why' }">Why Choose Us</a>
        <a href="#pricing" :class="{ active: activeSection === 'pricing' }">Pricing Tier</a>
        <a href="#checkout" :class="{ active: activeSection === 'checkout' }">Checkout system</a>
        <a href="#faq" :class="{ active: activeSection === 'faq' }">FAQ</a>
        <a href="#support" :class="{ active: activeSection === 'support' }">Support Contact</a>
      </nav>
      <div class="nav-actions">
        <button class="theme-toggle" @click="toggleTheme" :aria-label="isDark ? 'Switch to light mode' : 'Switch to dark mode'">
          <svg v-if="!isDark" xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="5"/><line x1="12" y1="1" x2="12" y2="3"/><line x1="12" y1="21" x2="12" y2="23"/><line x1="4.22" y1="4.22" x2="5.64" y2="5.64"/><line x1="18.36" y1="18.36" x2="19.78" y2="19.78"/><line x1="1" y1="12" x2="3" y2="12"/><line x1="21" y1="12" x2="23" y2="12"/><line x1="4.22" y1="19.78" x2="5.64" y2="18.36"/><line x1="18.36" y1="5.64" x2="19.78" y2="4.22"/></svg>
          <svg v-else xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M21 12.79A9 9 0 1 1 11.21 3 7 7 0 0 0 21 12.79z"/></svg>
        </button>
        <a class="btn ghost" href="#support">Hubungi Support</a>
        <a class="btn primary" href="#pricing">Order Sekarang</a>
      </div>
      </div>
    </header>

    <main>
      <section class="hero">
        <div class="hero-content reveal">
          <p class="eyebrow">Work Hour 08:00 - 21:00 WIB</p>
          <h1>Hosting cepat, stabil, dan ramah komunitas untuk semua kebutuhan.</h1>
          <p class="lead">
            Aisbir Cloud Nusantara menghadirkan layanan hosting dengan datacenter
            Indonesia, uptime tinggi, dan dukungan komunitas yang siap membantu.
          </p>
          <div class="hero-actions">
            <a class="btn primary" href="#pricing">Lihat Paket</a>
            <a class="btn ghost" href="#support">Kontak Support</a>
          </div>
        
        </div>
        <img 
          ref="heroImageRef"
          src="https://assets.aisbirnusantara.com/20260217_121832.png" 
          alt="Aisbir Cloud Nusantara" 
          class="hero-logo reveal" 
          :style="{
            transform: `perspective(1000px) rotateX(${imageTransform.rotateX}deg) rotateY(${imageTransform.rotateY}deg)`,
            transition: 'transform 0.1s ease-out'
          }"
        />
      </section>

      <section id="why" class="section">
        <div class="section-header reveal">
          <h2>Why Choose Us</h2>
          <p>Fokus pada performa, stabilitas, dan pengalaman terbaik di Indonesia.</p>
        </div>
        <div class="feature-grid">
          <article class="feature-card glass reveal delay-1">
            <h3>Indonesia Datacenter</h3>
            <p>Latensi rendah dengan infrastruktur lokal yang teruji.</p>
          </article>
          <article class="feature-card glass reveal delay-2">
            <h3>99% Uptime</h3>
            <p>Jaringan stabil untuk memastikan layanan selalu online.</p>
          </article>
          <article class="feature-card glass reveal delay-3">
            <h3>Harga Mahasiswa</h3>
            <p>Harga terjangkau khusus untuk mahasiswa Indonesia.</p>
          </article>
        </div>
      </section>

      <section id="pricing" class="section">
        <div class="section-header reveal">
          <h2>Pricing Tier</h2>
          <p>Paket fleksibel untuk kebutuhan personal hingga bisnis.</p>
        </div>
        <div class="pricing-table glass reveal">
          <div class="pricing-row pricing-header">
            <span>Tier</span>
            <span>Harga</span>
            <span>Bandwidth</span>
            <span>Storage</span>
            <span>CPU</span>
            <span>Domain</span>
            <span>SSL</span>
            <span>Aksi</span>
          </div>
          <div class="pricing-row">
            <span class="tier">Warga</span>
            <span>Rp 7.000</span>
            <span>Unlimited</span>
            <span>5 GB</span>
            <span>2</span>
            <span>2</span>
            <span class="status on">Enabled</span>
                     <button class="btn primary" @click="navigateToDash">Add To Cart</button>
          </div>
          <div class="pricing-row">
            <span class="tier">Pendekar</span>
            <span>Rp 15.000</span>
            <span>Unlimited</span>
            <span>15 GB</span>
            <span>3</span>
            <span>Unlimited</span>
              <span class="status on">Enabled</span>
              <button class="btn primary" @click="navigateToDash">Add To Cart</button>
          </div>
          <div class="pricing-row highlight">
            <span class="tier">Mahapatih</span>
            <span>Rp 20.000</span>
            <span>Unlimited</span>
            <span>20 GB</span>
            <span>4</span>
            <span>Unlimited</span>
            <span class="status on">Enabled</span>
                   <button class="btn primary" @click="navigateToDash">Add To Cart</button>
          </div>
          <div class="pricing-row">
            <span class="tier">Jendral</span>
            <span>Rp 50.000</span>
            <span>Unlimited</span>
            <span>50 GB</span>
            <span>10</span>
            <span>Unlimited</span>
            <span class="status on">Enabled</span>
                       <button class="btn primary" @click="navigateToDash">Add To Cart</button>
          </div>
          <div class="pricing-row">
            <span class="tier">Pelajar</span>
            <span>Rp 5.000</span>
            <span>Unlimited</span>
            <span>2 GB</span>
            <span>1</span>
            <span>2</span>
            <span class="status on">Enabled</span>
                     <button class="btn primary" @click="navigateToDash">Add To Cart</button>
          </div>
        </div>
      </section>

      <section id="checkout" class="section">
        <div class="section-header reveal">
          <p class="eyebrow">Checkout system</p>
          <h2>Cara Kerja Website Kita</h2>
          <p>Kamu bingung gimana cara kerja website kita? Sini mimin kasih tau</p>
        </div>
        <div class="flow reveal">
          <article class="flow-step glass">
            <h3>Buat Akun</h3>
            <p>Masukkan Nama, Email, Dan password kamu!</p>
          </article>
          <article class="flow-step glass">
            <h3>Pilih Produk</h3>
            <p>Pilih apapun yang kamu butuhkan, Jika yang kamu butuhkan tidak ada hubungi di livechat.</p>
          </article>
          <article class="flow-step glass">
            <h3>Checkout</h3>
            <p>Pilih metode pembayaran dan lakukan checkout, lalu cek emailmu secara berkala.</p>
          </article>
        </div>
      </section>

      <section id="faq" class="section">
        <div class="section-header reveal">
          <h2>FAQ</h2>
          <p>Jawaban singkat untuk pertanyaan yang sering masuk.</p>
        </div>
        <div class="faq-list reveal">
          <details class="faq-item glass">
            <summary>Apakah tersedia migrasi gratis?</summary>
            <p>Ya, tim kami membantu migrasi dasar untuk website yang aktif.</p>
          </details>
          <details class="faq-item glass">
            <summary>Metode pembayaran apa saja yang tersedia?</summary>
            <p>Kami menerima transfer bank, e-wallet, dan virtual account.</p>
          </details>
          <details class="faq-item glass">
            <summary>Bagaimana cara menghubungi support?</summary>
            <p>Gunakan WhatsApp atau Telegram pada bagian Support Contact.</p>
          </details>
            <details class="faq-item glass">
            <summary>Kok bisa murah?, Bukan home server / Jalur ilegal kan</summary>
            <p>Server Kami memiliki legalitas dan keamanan yang terjamin, untuk backend website tetap di home server kami, tetapi hosting yang dibuat berada pada datacenter jakarta dan Europa.</p>
          </details>
              <details class="faq-item glass">
            <summary>Saya masih awam, apakah saya bisa membuat website?</summary>
            <p>Tentu Server Kami dibekali dengan wordpress yang mudah digunakan orang awam.</p>
          </details>
        </div>
      </section>

      <section id="support" class="support reveal">
        <div class="support-content">
          <h2>Support Contact</h2>
          <p>Pilih jalur kontak yang paling nyaman untuk Anda.</p>
          <div class="cta-actions">
            <a class="btn primary" href="https://wa.me/6285173118646" target="_blank" rel="noreferrer">WhatsApp Support</a>
            <a class="btn ghost" href="https://t.me/aisbirnusantara" target="_blank" rel="noreferrer">Telegram Support</a>
          </div>
        </div>
        <img class="support-image" src="/support.svg" alt="Support" />
      </section>

      <section class="payment-methods section">
        <div class="section-header reveal" style="text-align: center; margin: 0 auto; display: flex; flex-direction: column; align-items: center;">

            <h2 style="font-size: 1.5rem;">Metode Pembayaran</h2>
            <p style="font-size: 0.9rem;margin-top: -3px;">Berbagai pilihan pembayaran untuk kemudahan transaksi Anda</p>
        </div>
        <div class="payment-carousel" style="margin-top: -5px;">
          <div class="payment-track payment-track-forward">
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/VA.PNG" alt="Maybank VA" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/RETAIL.PNG" alt="Retail" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/PERMATA.PNG" alt="Permata VA" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/B1.PNG" alt="CIMB Niaga VA" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/A1.PNG" alt="ATM Bersama VA" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/I1.PNG" alt="BNI VA" />
            </div>
            <div class="payment-item">
              <img src="https://cdn.aisbirnusantara.com/BpE4BPVyIw1605597490.png" alt="QRIS" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/S1.JPG" alt="Sampoerna VA" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/MV.PNG" alt="Mandiri VA" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/BR.PNG" alt="BRI VA" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/NC.PNG" alt="BNC VA" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/DM.PNG" alt="Danamon VA" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/BSI.PNG" alt="BSI VA" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/OV.PNG" alt="OVO" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/SHOPEEPAY.PNG" alt="ShopeePay" />
            </div>
            <!-- Duplicate for seamless loop -->
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/VA.PNG" alt="Maybank VA" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/RETAIL.PNG" alt="Retail" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/PERMATA.PNG" alt="Permata VA" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/B1.PNG" alt="CIMB Niaga VA" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/A1.PNG" alt="ATM Bersama VA" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/I1.PNG" alt="BNI VA" />
            </div>
            <div class="payment-item">
              <img src="https://cdn.aisbirnusantara.com/BpE4BPVyIw1605597490.png" alt="QRIS" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/S1.JPG" alt="Sampoerna VA" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/MV.PNG" alt="Mandiri VA" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/BR.PNG" alt="BRI VA" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/NC.PNG" alt="BNC VA" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/DM.PNG" alt="Danamon VA" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/BSI.PNG" alt="BSI VA" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/OV.PNG" alt="OVO" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/SHOPEEPAY.PNG" alt="ShopeePay" />
            </div>
          </div>
        </div>
        <div class="payment-carousel payment-carousel-reverse" style="margin-top: -20px;">
          <div class="payment-track payment-track-reverse">
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/VA.PNG" alt="Maybank VA" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/RETAIL.PNG" alt="Retail" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/PERMATA.PNG" alt="Permata VA" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/B1.PNG" alt="CIMB Niaga VA" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/A1.PNG" alt="ATM Bersama VA" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/I1.PNG" alt="BNI VA" />
            </div>
            <div class="payment-item">
              <img src="https://cdn.aisbirnusantara.com/BpE4BPVyIw1605597490.png" alt="QRIS" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/S1.JPG" alt="Sampoerna VA" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/MV.PNG" alt="Mandiri VA" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/BR.PNG" alt="BRI VA" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/NC.PNG" alt="BNC VA" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/DM.PNG" alt="Danamon VA" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/BSI.PNG" alt="BSI VA" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/OV.PNG" alt="OVO" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/SHOPEEPAY.PNG" alt="ShopeePay" />
            </div>
            <!-- Duplicate for seamless loop -->
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/VA.PNG" alt="Maybank VA" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/RETAIL.PNG" alt="Retail" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/PERMATA.PNG" alt="Permata VA" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/B1.PNG" alt="CIMB Niaga VA" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/A1.PNG" alt="ATM Bersama VA" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/I1.PNG" alt="BNI VA" />
            </div>
            <div class="payment-item">
              <img src="https://cdn.aisbirnusantara.com/BpE4BPVyIw1605597490.png" alt="QRIS" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/S1.JPG" alt="Sampoerna VA" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/MV.PNG" alt="Mandiri VA" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/BR.PNG" alt="BRI VA" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/NC.PNG" alt="BNC VA" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/DM.PNG" alt="Danamon VA" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/BSI.PNG" alt="BSI VA" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/OV.PNG" alt="OVO" />
            </div>
            <div class="payment-item">
              <img src="https://images.duitku.com/hotlink-ok/SHOPEEPAY.PNG" alt="ShopeePay" />
            </div>
          </div>
        </div>
      </section>
    </main>

    <footer class="footer">
      <div class="footer-container">
        <div class="footer-section">
          <h3 class="footer-heading">Our Products</h3>
          <ul class="footer-list">
            <li><a href="https://dash.aisbirnusantara.com">Hosting Indonesia</a></li>
            <li><a href="https://dash.aisbirnusantara.com">Hosting Europa</a></li>
            <li><a href="https://dash.aisbirnusantara.com">Directadmin Hosting</a></li>
            <li><a href="https://dash.aisbirnusantara.com">Student Hosting</a></li>
          </ul>
        </div>
        <div class="footer-section">
          <h3 class="footer-heading">Resources</h3>
          <ul class="footer-list">
            <li><a href="https://dash.aisbirnusantara.com" target="_blank" rel="noreferrer">Client Area</a></li>
            <li><a href="https://youtube.com/@aisbirkoenz">Daftar Tutorial</a></li>
            <li><a href="https://dash.aisbirnusantara.com/terms">Terms of service</a></li>
            <li><a href="https://dash.aisbirnusantara.com/privacy">Privacy Policy</a></li>
          </ul>
        </div>
        <div class="footer-section">
          <h3 class="footer-heading">Support</h3>
          <ul class="footer-list">
            <li><a href="https://wa.me/6285173118646" target="_blank" rel="noreferrer">Whatsapp</a></li>
            <li><a href="https://t.me/aisbirnusantara" target="_blank" rel="noreferrer">Telegram</a></li>
            <li><a href="https://t.me/tokoaisbirnusantara" target="_blank" rel="noreferrer">Update Group</a></li>
            <li><a href="https://www.instagram.com/aisbirnusantara" target="_blank" rel="noreferrer">Instagram</a></li>
            <li><a href="https://www.threads.net/@aisbirnusantara" target="_blank" rel="noreferrer">Threads</a></li>
          </ul>
        </div>
        <div class="footer-section footer-brand">
          <img src="/logoku.png" alt="Aisbir Cloud Nusantara" class="footer-logo" />
          <p class="footer-company">Aisbir Cloud Nusantara</p>
          <p class="footer-contact">Contact support@aisbirnusantara.com</p>
          <p class="footer-hours">Setiap Hari, 08:00 - 21:00 WIB</p>
        </div>
      </div>
      <div class="footer-bottom">
        <p>Copyright © 2026. All Rights Reserved To Aisbir Cloud Nusantara</p>
      </div>
    </footer>
  </div>
</template>

<style scoped>


.hero {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 40px;
  align-items: center;
  padding: 40px 24px 60px;
  width: min(1200px, 100%);
  margin: 0 auto;
}

.hero-content h1 {
  font-size: clamp(2.2rem, 3vw, 3.4rem);
  line-height: 1.15;
  margin: 20px 0;
  color: var(--ink);
}

.lead {
  font-size: 1rem;
  color: var(--muted);
  margin: 0 0 24px;
  line-height: 1.6;
}

.eyebrow {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  padding: 6px 14px;
  border-radius: 999px;
  background: rgba(0, 188, 212, 0.1);
  color: var(--accent-2);
  font-size: 0.85rem;
  font-weight: 600;
}

.hero-actions {
  display: flex;
  flex-wrap: wrap;
  gap: 12px;
  margin-bottom: 28px;
}


@keyframes fadeUp {
  from {
    opacity: 0;
    transform: translateY(16px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@media (max-width: 900px) {


  .hero {
    padding-top: 20px;
    gap: 30px;
  }
}

@media (max-width: 600px) {
  .hero {
    grid-template-columns: 1fr;
  }

}
</style>
