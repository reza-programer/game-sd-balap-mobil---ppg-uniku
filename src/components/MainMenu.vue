<script setup>
import raceTrackImg from '../assets/race_track.png';
import { ref } from 'vue';

const emit = defineEmits(['start-game']);

const photoSrc   = new URL('../assets/roysita.jpg', import.meta.url).href;
const photoError = ref(false);
</script>

<template>
  <div class="main-menu">
    <div class="bg-overlay"></div>

    <div class="menu-layout">

      <!-- ═══════════════════════════════════════════ -->
      <!-- LEFT: Nametag ID Card                       -->
      <!-- ═══════════════════════════════════════════ -->
      <div class="nametag-section">

        <!-- Lanyard cord -->
        <div class="lanyard-cord">
          <div class="cord-left"></div>
          <div class="cord-right"></div>
        </div>

        <!-- Lanyard clip -->
        <div class="lanyard-clip">
          <div class="clip-body"></div>
          <div class="clip-hole"></div>
        </div>

        <!-- ID Card -->
        <div class="id-card">

          <!-- Decorative top stripe with logo/title -->
          <div class="card-header">
            <div class="header-logo">🎓</div>
            <div class="header-text">
              <div class="header-school">PPG UNIKU 2026</div>
              <div class="header-subtitle">PEMBUAT GAME EDUKASI</div>
            </div>
          </div>

          <!-- Thin colored line -->
          <div class="card-accent-line"></div>

          <!-- Photo section -->
          <div class="card-photo-section">
            <div class="photo-frame">
              <img
                v-if="!photoError"
                :src="photoSrc"
                alt="Roysita"
                class="card-photo"
                @error="photoError = true"
              />
              <div v-else class="card-initials">RS</div>
            </div>
          </div>

          <!-- Name & Info -->
          <div class="card-body">
            <h2 class="card-name">Rosita</h2>
            <div class="card-title-badge">PPG UNIKU</div>

            <div class="card-divider"></div>

            <div class="card-details">
              <div class="card-detail-row">
                <span class="detail-icon">🎮</span>
                <span>Game Developer</span>
              </div>
              <div class="card-detail-row">
                <span class="detail-icon">📚</span>
                <span>Balap Perkalian</span>
              </div>
              <div class="card-detail-row">
                <span class="detail-icon">🏫</span>
                <span>Sekolah Dasar</span>
              </div>
            </div>
          </div>

          <!-- Social media footer -->
          <div class="card-footer">
            <div class="social-row">
              <!-- LinkedIn -->
              <a
                class="social-btn linkedin"
                title="LinkedIn"
                href="https://www.linkedin.com/in/rositaaaa?utm_source=share_via&utm_content=profile&utm_medium=member_ios"
                target="_blank"
                rel="noopener noreferrer"
                @click.stop
              >
                <svg viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                  <path d="M20.447 20.452h-3.554v-5.569c0-1.328-.027-3.037-1.852-3.037-1.853 0-2.136 1.445-2.136 2.939v5.667H9.351V9h3.414v1.561h.046c.477-.9 1.637-1.85 3.37-1.85 3.601 0 4.267 2.37 4.267 5.455v6.286zM5.337 7.433a2.062 2.062 0 0 1-2.063-2.065 2.064 2.064 0 1 1 2.063 2.065zm1.782 13.019H3.555V9h3.564v11.452zM22.225 0H1.771C.792 0 0 .774 0 1.729v20.542C0 23.227.792 24 1.771 24h20.451C23.2 24 24 23.227 24 22.271V1.729C24 .774 23.2 0 22.222 0h.003z"/>
                </svg>
              </a>
              <!-- Instagram -->
              <a
                class="social-btn instagram"
                title="Instagram"
                href="https://www.instagram.com/ocytaaa?igsh=ejUzdmM1ZXlibG96"
                target="_blank"
                rel="noopener noreferrer"
                @click.stop
              >
                <svg viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                  <path d="M12 2.163c3.204 0 3.584.012 4.85.07 3.252.148 4.771 1.691 4.919 4.919.058 1.265.069 1.645.069 4.849 0 3.205-.012 3.584-.069 4.849-.149 3.225-1.664 4.771-4.919 4.919-1.266.058-1.644.07-4.85.07-3.204 0-3.584-.012-4.849-.07-3.26-.149-4.771-1.699-4.919-4.92-.058-1.265-.07-1.644-.07-4.849 0-3.204.013-3.583.07-4.849.149-3.227 1.664-4.771 4.919-4.919 1.266-.057 1.645-.069 4.849-.069zM12 0C8.741 0 8.333.014 7.053.072 2.695.272.273 2.69.073 7.052.014 8.333 0 8.741 0 12c0 3.259.014 3.668.072 4.948.2 4.358 2.618 6.78 6.98 6.98C8.333 23.986 8.741 24 12 24c3.259 0 3.668-.014 4.948-.072 4.354-.2 6.782-2.618 6.979-6.98.059-1.28.073-1.689.073-4.948 0-3.259-.014-3.667-.072-4.947-.196-4.354-2.617-6.78-6.979-6.98C15.668.014 15.259 0 12 0zm0 5.838a6.162 6.162 0 1 0 0 12.324 6.162 6.162 0 0 0 0-12.324zM12 16a4 4 0 1 1 0-8 4 4 0 0 1 0 8zm6.406-11.845a1.44 1.44 0 1 0 0 2.881 1.44 1.44 0 0 0 0-2.881z"/>
                </svg>
              </a>
              <!-- TikTok -->
              <a
                class="social-btn tiktok"
                title="TikTok"
                href="https://www.tiktok.com/@fl0ovy3s?_r=1&_t=ZS-95PlOv9Ccj3"
                target="_blank"
                rel="noopener noreferrer"
                @click.stop
              >
                <svg viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                  <path d="M12.525.02c1.31-.02 2.61-.01 3.91-.02.08 1.53.63 3.09 1.75 4.17 1.12 1.11 2.7 1.62 4.24 1.79v4.03c-1.44-.05-2.89-.35-4.2-.97-.57-.26-1.1-.59-1.62-.93-.01 2.92.01 5.84-.02 8.75-.08 1.4-.54 2.79-1.35 3.94-1.31 1.92-3.58 3.17-5.91 3.21-1.43.08-2.86-.31-4.08-1.03-2.02-1.19-3.44-3.37-3.65-5.71-.02-.5-.03-1-.01-1.49.18-1.9 1.12-3.72 2.58-4.96 1.66-1.44 3.98-2.13 6.15-1.72.02 1.48-.04 2.96-.04 4.44-.99-.32-2.15-.23-3.02.37-.63.41-1.11 1.04-1.36 1.75-.21.51-.15 1.07-.14 1.61.24 1.64 1.82 3.02 3.5 2.87 1.12-.01 2.19-.66 2.77-1.61.19-.33.4-.67.41-1.06.1-1.79.06-3.57.07-5.36.01-4.03-.01-8.05.02-12.07z"/>
                </svg>
              </a>
            </div>
            <div class="social-label">Follow Rosita</div>
          </div>

        </div>
      </div>

      <!-- ═══════════════════════════════════════════ -->
      <!-- RIGHT: Title + Game Showcase                -->
      <!-- ═══════════════════════════════════════════ -->
      <div class="right-section">

        <!-- Title -->
        <div class="title-block">
          <div class="car-icon">🏎️</div>
          <h1>Balap Perkalian</h1>
          <p class="tagline">Ayo Belajar Perkalian Sambil Balapan!</p>
        </div>

        <!-- Game showcase -->
        <div class="game-showcase">
          <div class="showcase-glow"></div>
          <div class="showcase-image" :style="{ backgroundImage: `url(${raceTrackImg})` }"></div>
          <div class="showcase-info">
            <h2>🏁 Balap Mobil</h2>
            <p>
              Jawab perkalian lebih cepat<br>
              dari lawanmu. Siapa duluan<br>
              menyentuh garis finish?
            </p>
            <div class="features">
              <span class="feat">⭐ 4 Level</span>
              <span class="feat">👥 1 vs 1</span>
              <span class="feat">🚀 Real-time</span>
            </div>
            <button class="btn-main-start" @click="emit('start-game')">
              Mulai Balapan! <span class="arrow">→</span>
            </button>
          </div>
        </div>

        <div class="footer-tag">Game Edukasi Matematika SD 🎓</div>
      </div>

    </div>
  </div>
</template>

<style scoped>
/* ────────────────── LAYOUT ────────────────── */
.main-menu {
  width: 100vw; height: 100vh;
  position: relative; overflow: hidden;
  display: flex; align-items: center; justify-content: center;
  font-family: 'Fredoka', sans-serif;
  background: linear-gradient(135deg, #1a1a2e 0%, #16213e 55%, #0f3460 100%);
}

.bg-overlay {
  position: absolute; inset: 0; pointer-events: none;
  background:
    radial-gradient(circle at 15% 30%, rgba(255,152,0,0.12) 0%, transparent 50%),
    radial-gradient(circle at 85% 70%, rgba(33,150,243,0.12) 0%, transparent 50%);
  animation: bgPulse 6s ease-in-out infinite alternate;
}
@keyframes bgPulse { from { opacity: 0.5; } to { opacity: 1; } }

.menu-layout {
  position: relative; z-index: 10;
  display: flex; align-items: center;
  gap: 52px;
  width: 92%; max-width: 1100px;
  height: 90vh;
  max-height: 680px;
}

/* ────────────────── NAMETAG SECTION ────────────────── */
.nametag-section {
  display: flex;
  flex-direction: column;
  align-items: center;
  flex-shrink: 0;
  animation: nametag-swing 4s ease-in-out infinite alternate;
  transform-origin: top center;
}
@keyframes nametag-swing {
  from { transform: rotate(-2deg) translateY(0);  }
  to   { transform: rotate(2deg)  translateY(-8px); }
}

/* ── Lanyard Cord ── */
.lanyard-cord {
  display: flex;
  justify-content: center;
  gap: 36px;
  height: 60px;
  position: relative;
  z-index: 1;
}
.cord-left, .cord-right {
  width: 4px;
  height: 100%;
  border-radius: 2px;
  background: linear-gradient(180deg, #8B6914 0%, #FFC107 40%, #8B6914 100%);
  box-shadow: 0 0 6px rgba(255,193,7,0.4);
}
.cord-left  { transform: rotate(8deg);  transform-origin: top center; }
.cord-right { transform: rotate(-8deg); transform-origin: top center; }

/* ── Clip / hook ── */
.lanyard-clip {
  position: relative;
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-top: -8px;
  z-index: 2;
}
.clip-body {
  width: 28px; height: 16px;
  background: linear-gradient(180deg, #aaa 0%, #ddd 50%, #aaa 100%);
  border-radius: 6px 6px 0 0;
  border: 2px solid #888;
  box-shadow: 0 2px 6px rgba(0,0,0,0.4);
}
.clip-hole {
  width: 14px; height: 8px;
  background: transparent;
  border: 3px solid #999;
  border-top: none;
  border-radius: 0 0 8px 8px;
  margin-top: -2px;
}

/* ── The ID Card itself ── */
.id-card {
  width: 230px;
  background: white;
  border-radius: 16px;
  overflow: hidden;
  box-shadow:
    0 24px 60px rgba(0,0,0,0.7),
    0 0 0 4px rgba(255,255,255,0.1);
  position: relative;
  z-index: 2;
}

/* Top header */
.card-header {
  background: linear-gradient(135deg, #FF5722, #FF8F00);
  padding: 14px 16px 12px;
  display: flex; align-items: center; gap: 10px;
}
.header-logo { font-size: 2rem; filter: drop-shadow(0 2px 4px rgba(0,0,0,0.3)); }
.header-text { display: flex; flex-direction: column; }
.header-school {
  font-size: 0.7rem; font-weight: 900;
  color: white; letter-spacing: 1.5px;
  text-shadow: 0 1px 2px rgba(0,0,0,0.4);
}
.header-subtitle {
  font-size: 0.6rem; color: rgba(255,255,255,0.8);
  letter-spacing: 1px;
}

.card-accent-line {
  height: 5px;
  background: linear-gradient(90deg, #FFC107, #FF5722, #FFC107);
}

/* Photo */
.card-photo-section {
  display: flex; justify-content: center;
  padding: 20px 16px 10px;
}
.photo-frame {
  width: 110px; height: 110px;
  border-radius: 12px;
  border: 5px solid #FF5722;
  overflow: hidden;
  box-shadow: 0 6px 20px rgba(255,87,34,0.4), 0 0 0 3px rgba(255,193,7,0.3);
  background: linear-gradient(135deg, #FF5722, #FFC107);
  display: flex; align-items: center; justify-content: center;
}
.card-photo {
  width: 100%; height: 100%;
  object-fit: cover; object-position: top;
}
.card-initials {
  font-size: 2.5rem; font-weight: 900;
  color: white; letter-spacing: 3px;
}

/* Body */
.card-body {
  padding: 10px 18px 14px;
  text-align: center;
}
.card-name {
  font-size: 1.6rem; margin: 0 0 6px;
  color: #1a1a1a; font-weight: 900;
}
.card-title-badge {
  display: inline-block;
  background: linear-gradient(135deg, #FF5722, #FF8F00);
  color: white; font-size: 0.75rem; font-weight: 700;
  padding: 4px 14px; border-radius: 20px;
  letter-spacing: 0.5px;
  box-shadow: 0 2px 8px rgba(255,87,34,0.35);
}
.card-divider {
  height: 2px; margin: 12px auto;
  background: linear-gradient(90deg, transparent, #eee, transparent);
}
.card-details {
  display: flex; flex-direction: column; gap: 6px;
  text-align: left;
}
.card-detail-row {
  display: flex; align-items: center; gap: 8px;
  font-size: 0.85rem; color: #555;
}
.detail-icon { font-size: 1rem; flex-shrink: 0; }

/* Social footer */
.card-footer {
  background: #f4f4f4;
  border-top: 1px solid #eee;
  padding: 12px 16px 10px;
  display: flex; flex-direction: column; align-items: center; gap: 6px;
}

.social-row {
  display: flex; gap: 10px; align-items: center; justify-content: center;
}

.social-btn {
  width: 36px; height: 36px;
  border-radius: 10px;
  display: flex; align-items: center; justify-content: center;
  cursor: pointer; transition: transform 0.2s, box-shadow 0.2s;
  text-decoration: none;
}
.social-btn svg {
  width: 18px; height: 18px;
  fill: white;
}
.social-btn:hover {
  transform: translateY(-3px) scale(1.1);
  box-shadow: 0 6px 14px rgba(0,0,0,0.25);
}

/* Brand colors */
.linkedin  { background: #0A66C2; box-shadow: 0 3px 0 #084d91; }
.instagram {
  background: radial-gradient(circle at 30% 110%, #fdf497, #fe820a 25%, #fd1d1d 50%, #e1306c 65%, #833ab4 80%, #c13584 100%);
  box-shadow: 0 3px 0 #be185d;
}
.tiktok    { background: #010101; box-shadow: 0 3px 0 #333; }

.social-label {
  font-size: 0.62rem; color: #aaa;
  letter-spacing: 1.5px; font-weight: 600;
  text-transform: uppercase;
}

/* ────────────────── RIGHT SECTION ────────────────── */
.right-section {
  flex: 1;
  display: flex; flex-direction: column;
  justify-content: center;
  gap: 20px;
}

.title-block { color: white; }
.car-icon {
  font-size: 3.5rem; display: block;
  animation: carBounce 1.5s ease-in-out infinite alternate;
  filter: drop-shadow(0 6px 12px rgba(255,150,0,0.5));
}
@keyframes carBounce {
  from { transform: translateY(0) rotate(-3deg); }
  to   { transform: translateY(-10px) rotate(3deg); }
}
.title-block h1 {
  font-size: 3.2rem; margin: 6px 0 4px;
  background: linear-gradient(135deg, #FFC107, #FF5722, #E91E63);
  -webkit-background-clip: text; -webkit-text-fill-color: transparent;
  background-clip: text;
  filter: drop-shadow(0 4px 8px rgba(255,87,34,0.3));
}
.tagline { font-size: 1.1rem; color: rgba(255,255,255,0.65); margin: 0; }

/* Showcase */
.game-showcase {
  background: rgba(255,255,255,0.06);
  backdrop-filter: blur(16px);
  border: 2px solid rgba(255,255,255,0.1);
  border-radius: 22px;
  display: flex; overflow: hidden;
  position: relative;
  transition: all 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
  box-shadow: 0 16px 50px rgba(0,0,0,0.5);
}
.game-showcase:hover {
  transform: translateY(-6px) scale(1.01);
  border-color: rgba(255,193,7,0.4);
  box-shadow: 0 24px 60px rgba(0,0,0,0.6), 0 0 30px rgba(255,193,7,0.1);
}
.showcase-glow {
  position: absolute; inset: 0; pointer-events: none;
  background: linear-gradient(135deg, rgba(255,255,255,0.07) 0%, transparent 55%);
}
.showcase-image {
  width: 38%; min-height: 200px;
  background-size: cover; background-position: center;
  background-color: #1a1a2e; position: relative;
}
.showcase-image::after {
  content: ''; position: absolute; inset: 0;
  background: linear-gradient(90deg, transparent 60%, rgba(15,20,40,0.95) 100%);
}
.showcase-info {
  flex: 1; padding: 24px 30px;
  display: flex; flex-direction: column; justify-content: center; gap: 10px;
  color: white;
}
.showcase-info h2 { font-size: 1.9rem; margin: 0; }
.showcase-info p  { font-size: 1rem; line-height: 1.6; color: rgba(255,255,255,0.8); margin: 0; }
.features { display: flex; flex-wrap: wrap; gap: 7px; }
.feat {
  background: rgba(255,193,7,0.18);
  border: 1px solid rgba(255,193,7,0.4);
  padding: 3px 12px; border-radius: 20px;
  font-size: 0.9rem; color: #FFC107; font-weight: 600;
}
.btn-main-start {
  align-self: flex-start;
  background: linear-gradient(135deg, #FFC107, #FF5722);
  border: none; color: white; padding: 12px 30px;
  border-radius: 999px; cursor: pointer;
  font-family: 'Fredoka', sans-serif; font-size: 1.3rem; font-weight: bold;
  box-shadow: 0 6px 0 #c44d00, 0 10px 20px rgba(255,87,34,0.35);
  transition: all 0.15s; display: flex; align-items: center; gap: 8px;
}
.btn-main-start:hover  { transform: translateY(-4px); box-shadow: 0 10px 0 #c44d00, 0 14px 28px rgba(255,87,34,0.4); }
.btn-main-start:active { transform: translateY(6px);  box-shadow: none; }
.arrow { display: inline-block; transition: transform 0.2s; }
.btn-main-start:hover .arrow { transform: translateX(5px); }

.footer-tag { color: rgba(255,255,255,0.28); font-size: 0.9rem; }
</style>
