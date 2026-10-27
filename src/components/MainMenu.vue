<script setup>
import raceTrackImg from '../assets/race_track.png';
import logoUniku    from '../assets/logo-uniku.png';
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

        <!-- Lanyard strap -->
        <div class="lanyard">
          <div class="strap strap-left"></div>
          <div class="strap strap-right"></div>
        </div>

        <!-- Metal clip -->
        <div class="lanyard-clip">
          <div class="clip-ring"></div>
          <div class="clip-body"></div>
        </div>

        <!-- ID Card -->
        <div class="id-card">
          <div class="card-slot"></div>

          <!-- University identity -->
          <div class="card-header">
            <img :src="logoUniku" alt="Logo Universitas Kuningan" class="uni-logo" />
            <div class="header-text">
              <div class="header-school">Universitas<br />Kuningan</div>
              <div class="header-subtitle">Pendidikan Profesi Guru</div>
            </div>
          </div>
          <div class="card-stripe"></div>

          <!-- Photo + name -->
          <div class="card-identity" :style="{ '--watermark': `url(${logoUniku})` }">
            <div class="photo-frame">
              <img
                v-if="!photoError"
                :src="photoSrc"
                alt="Rosita"
                class="card-photo"
                @error="photoError = true"
              />
              <div v-else class="card-initials">RS</div>
            </div>

            <h2 class="card-name">Rosita</h2>
            <div class="card-role">Pembuat Game Edukasi</div>
            <div class="card-badge">PPG UNIKU 2026</div>
          </div>

          <!-- Details -->
          <dl class="card-details">
            <div class="card-detail-row">
              <dt>Fakultas</dt>
              <dd>FKIP</dd>
            </div>
            <div class="card-detail-row">
              <dt>Peran</dt>
              <dd>Mahasiswa PPG</dd>
            </div>
            <div class="card-detail-row">
              <dt>Game</dt>
              <dd>Balap Perkalian</dd>
            </div>
            <div class="card-detail-row">
              <dt>Jenjang</dt>
              <dd>Sekolah Dasar</dd>
            </div>
          </dl>

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
          <div class="eyebrow">Game Edukasi Matematika · Sekolah Dasar</div>
          <div class="title-row">
            <h1>Balap Perkalian</h1>
            <span class="car-icon">🏎️</span>
          </div>
          <p class="tagline">Ayo belajar perkalian sambil balapan!</p>
        </div>

        <!-- Game showcase -->
        <div class="game-showcase">
          <div class="showcase-image" :style="{ backgroundImage: `url(${raceTrackImg})` }"></div>
          <div class="showcase-info">
            <h2>Cara Bermain</h2>
            <p>Jawab perkalian lebih cepat dari lawanmu. Siapa duluan menyentuh garis finish?</p>

            <ul class="stats">
              <li><strong>4</strong><span>Level</span></li>
              <li><strong>1 vs 1</strong><span>Tim</span></li>
              <li><strong>1–10</strong><span>Angka</span></li>
            </ul>

            <button class="btn-main-start" @click="emit('start-game')">
              Mulai Balapan <span class="arrow">→</span>
            </button>
          </div>
        </div>
      </div>

    </div>
  </div>
</template>

<style scoped>
/* ────────────────── LAYOUT ────────────────── */
.main-menu {
  width: 100%; height: 100%;
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
  gap: 3.25rem;
  width: 92%; max-width: 68.75rem;
  height: 90%;
  max-height: 42.5rem;
}

/* ────────────────── NAMETAG SECTION ────────────────── */
/* Kartu identitas bergaya kartu kampus: warna Universitas Kuningan
   (biru-tosca #006680 + kuning #FEFF67 dari logo), huruf Plus Jakarta Sans. */
.nametag-section {
  --uniku: #006680;
  --uniku-dark: #004a5e;
  --uniku-yellow: #FEFF67;
  --ink: #0f2530;
  --muted: #73848d;

  display: flex;
  flex-direction: column;
  align-items: center;
  flex-shrink: 0;
  font-family: 'Plus Jakarta Sans', 'Fredoka', sans-serif;
  animation: nametag-swing 5s ease-in-out infinite alternate;
  transform-origin: top center;
}
@keyframes nametag-swing {
  from { transform: rotate(-1.2deg); }
  to   { transform: rotate(1.2deg) translateY(-0.25rem); }
}

/* ── Lanyard: two ribbons meeting at the clip ── */
.lanyard {
  position: relative;
  width: 6rem; height: 4.5rem;
  -webkit-mask-image: linear-gradient(to top, black 55%, transparent);
  mask-image: linear-gradient(to top, black 55%, transparent);
}
.strap {
  position: absolute; bottom: 0; left: 50%;
  width: 1rem; height: 5rem;
  background: linear-gradient(90deg, var(--uniku-dark), var(--uniku) 30%, var(--uniku) 70%, var(--uniku-dark));
  transform-origin: bottom center;
}
.strap::after {
  content: '';
  position: absolute; top: 0; bottom: 0; left: 50%;
  width: 0.125rem; transform: translateX(-50%);
  background: var(--uniku-yellow); opacity: 0.75;
}
.strap-left  { transform: translateX(-50%) rotate(18deg); }
.strap-right { transform: translateX(-50%) rotate(-18deg); }

/* ── Metal clip ── */
.lanyard-clip {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-top: -0.5rem;
  position: relative;
  z-index: 3;
}
.clip-ring {
  width: 1.25rem; height: 1.25rem;
  border-radius: 50%;
  border: 0.25rem solid #b9c3c9;
}
.clip-body {
  width: 2.25rem; height: 1.375rem;
  margin-top: -0.25rem;
  border-radius: 0.375rem 0.375rem 0.25rem 0.25rem;
  background: linear-gradient(180deg, #eef2f4, #a9b4ba);
  box-shadow: 0 0.125rem 0.375rem rgba(0,0,0,0.35);
}

/* ── The ID Card itself ── */
.id-card {
  width: 15.5rem;
  margin-top: -0.625rem;
  background: #fff;
  border-radius: 1rem;
  overflow: hidden;
  position: relative;
  box-shadow:
    0 1.5rem 3.5rem rgba(0,0,0,0.55),
    0 0 0 0.0625rem rgba(255,255,255,0.08);
}

/* Slot punched in the card where the clip goes through */
.card-slot {
  position: absolute; top: 0.375rem; left: 50%;
  transform: translateX(-50%);
  width: 2.75rem; height: 0.4375rem;
  border-radius: 99px;
  background: #dfe6ea;
  box-shadow: inset 0 0.0625rem 0.125rem rgba(0,0,0,0.25);
}

/* University identity */
.card-header {
  display: flex; align-items: center; gap: 0.75rem;
  padding: 1.25rem 1.125rem 0.75rem;
}
.uni-logo {
  width: 3rem; height: 3rem;
  object-fit: contain;
  flex-shrink: 0;
}
.header-text { display: flex; flex-direction: column; text-align: left; }
.header-school {
  font-size: 0.95rem; font-weight: 800; line-height: 1.05;
  color: var(--uniku);
  letter-spacing: 0.03em;
  text-transform: uppercase;
}
.header-subtitle {
  margin-top: 0.25rem;
  font-size: 0.66rem; font-weight: 600;
  color: var(--muted);
}

.card-stripe {
  height: 0.375rem;
  background: linear-gradient(180deg, var(--uniku) 0 65%, var(--uniku-yellow) 65% 100%);
}

/* Photo + name */
.card-identity {
  position: relative;
  display: flex; flex-direction: column; align-items: center;
  padding: 1.125rem 1rem 0.875rem;
  background: linear-gradient(180deg, rgba(0,102,128,0.07), rgba(0,102,128,0) 75%);
}
/* Faint emblem watermark behind the photo */
.card-identity::before {
  content: '';
  position: absolute; inset: 0;
  background: var(--watermark) center 38% / 10.5rem no-repeat;
  opacity: 0.045;
  pointer-events: none;
}
.photo-frame {
  position: relative;
  width: 6.5rem; height: 8.125rem;
  border-radius: 0.625rem;
  overflow: hidden;
  border: 0.1875rem solid #fff;
  box-shadow: 0 0 0 0.125rem var(--uniku), 0 0.5rem 1.25rem rgba(0,40,55,0.25);
  background: #dfe7ea;
  display: flex; align-items: center; justify-content: center;
}
.card-photo {
  width: 100%; height: 100%;
  object-fit: cover; object-position: center top;
}
.card-initials {
  font-size: 2.25rem; font-weight: 800;
  color: var(--uniku); letter-spacing: 0.1rem;
}
.card-name {
  margin: 0.875rem 0 0.125rem;
  font-size: 1.45rem; font-weight: 800; line-height: 1.15;
  color: var(--ink);
  letter-spacing: -0.01em;
}
.card-role {
  font-size: 0.7rem; font-weight: 700;
  color: var(--uniku);
  letter-spacing: 0.12em;
  text-transform: uppercase;
}
.card-badge {
  margin-top: 0.5rem;
  padding: 0.25rem 0.75rem;
  border-radius: 99px;
  background: var(--uniku-yellow);
  color: var(--ink);
  font-size: 0.66rem; font-weight: 800;
  letter-spacing: 0.06em;
}

/* Details as label · value rows */
.card-details {
  margin: 0;
  padding: 0.75rem 1.125rem 0.875rem;
  border-top: 0.0625rem dashed #d6dfe3;
  display: flex; flex-direction: column; gap: 0.375rem;
}
.card-detail-row {
  display: flex; justify-content: space-between; align-items: baseline; gap: 0.75rem;
  font-size: 0.78rem;
}
.card-detail-row dt { color: var(--muted); font-weight: 600; }
.card-detail-row dd { margin: 0; color: var(--ink); font-weight: 700; text-align: right; }

/* Social footer */
.card-footer {
  display: flex; align-items: center; justify-content: space-between;
  padding: 0.625rem 1.125rem 0.6875rem;
  background: #f3f6f8;
  border-top: 0.0625rem solid #e4eaed;
  border-bottom: 0.375rem solid var(--uniku);
}

.social-row {
  display: flex; gap: 0.4375rem; align-items: center;
}

.social-btn {
  width: 1.875rem; height: 1.875rem;
  border-radius: 0.5rem;
  display: flex; align-items: center; justify-content: center;
  cursor: pointer; transition: transform 0.2s, box-shadow 0.2s;
  text-decoration: none;
}
.social-btn svg {
  width: 0.9375rem; height: 0.9375rem;
  fill: white;
}
.social-btn:hover {
  transform: translateY(-0.125rem);
  box-shadow: 0 0.375rem 0.75rem rgba(0,0,0,0.2);
}

/* Brand colors */
.linkedin  { background: #0A66C2; }
.instagram {
  background: radial-gradient(circle at 30% 110%, #fdf497, #fe820a 25%, #fd1d1d 50%, #e1306c 65%, #833ab4 80%, #c13584 100%);
}
.tiktok    { background: #010101; }

.social-label {
  order: -1; /* label on the left, icons on the right */
  font-size: 0.62rem; font-weight: 700;
  color: var(--muted);
  letter-spacing: 0.12em;
  text-transform: uppercase;
}

/* ────────────────── RIGHT SECTION ────────────────── */
.right-section {
  flex: 1;
  min-width: 0;
  display: flex; flex-direction: column;
  justify-content: center;
  gap: 1.75rem;
}

/* Title */
.title-block { color: white; }
.eyebrow {
  font-family: 'Plus Jakarta Sans', 'Fredoka', sans-serif;
  font-size: 0.8rem; font-weight: 700;
  color: #FFC107;
  letter-spacing: 0.16em;
  text-transform: uppercase;
}
.title-row {
  display: flex; align-items: center; gap: 1rem;
  margin: 0.5rem 0 0.375rem;
}
.title-block h1 {
  font-size: 3.4rem; margin: 0; line-height: 1.05;
  background: linear-gradient(135deg, #FFC107, #FF5722 70%);
  -webkit-background-clip: text; -webkit-text-fill-color: transparent;
  background-clip: text;
}
.car-icon {
  font-size: 2.6rem; display: block;
  animation: carBounce 1.5s ease-in-out infinite alternate;
}
@keyframes carBounce {
  from { transform: translateY(0) rotate(-3deg); }
  to   { transform: translateY(-0.375rem) rotate(3deg); }
}
.tagline {
  margin: 0;
  font-family: 'Plus Jakarta Sans', 'Fredoka', sans-serif;
  font-size: 1.05rem; font-weight: 500;
  color: rgba(255,255,255,0.7);
}

/* Showcase */
.game-showcase {
  display: flex;
  overflow: hidden;
  border-radius: 1.25rem;
  background: rgba(255,255,255,0.05);
  border: 0.0625rem solid rgba(255,255,255,0.12);
  box-shadow: 0 1.25rem 3rem rgba(0,0,0,0.35);
  transition: border-color 0.2s;
}
.game-showcase:hover { border-color: rgba(255,193,7,0.35); }

.showcase-image {
  width: 42%;
  min-height: 13.5rem;
  background-size: cover; background-position: center;
  background-color: #1a1a2e;
  border-right: 0.0625rem solid rgba(255,255,255,0.12);
}

.showcase-info {
  flex: 1;
  padding: 1.75rem 2rem;
  display: flex; flex-direction: column; justify-content: center;
  color: white;
}
.showcase-info h2 {
  margin: 0 0 0.375rem;
  font-size: 1.4rem;
}
.showcase-info p {
  margin: 0;
  max-width: 19.5rem; /* breaks after "lawanmu." */
  font-family: 'Plus Jakarta Sans', 'Fredoka', sans-serif;
  font-size: 0.95rem; line-height: 1.6;
  color: rgba(255,255,255,0.72);
}

/* 3 facts in a row, split by thin dividers */
.stats {
  list-style: none;
  display: flex;
  margin: 1.25rem 0 1.5rem; padding: 0;
}
.stats li {
  display: flex; flex-direction: column;
  padding: 0 1.25rem;
  border-left: 0.0625rem solid rgba(255,255,255,0.14);
}
.stats li:first-child { padding-left: 0; border-left: none; }
.stats strong {
  font-size: 1.45rem; line-height: 1.15;
  color: #FFC107;
}
.stats span {
  font-family: 'Plus Jakarta Sans', 'Fredoka', sans-serif;
  font-size: 0.72rem; font-weight: 600;
  color: rgba(255,255,255,0.55);
  letter-spacing: 0.08em;
  text-transform: uppercase;
}

.btn-main-start {
  align-self: flex-start;
  display: flex; align-items: center; gap: 0.625rem;
  padding: 0.8rem 1.75rem;
  border: none; border-radius: 99px; cursor: pointer;
  background: linear-gradient(135deg, #FFC107, #FF6D00);
  color: #1f1300;
  font-family: 'Fredoka', sans-serif; font-size: 1.2rem; font-weight: 700;
  box-shadow: 0 0.5rem 1.25rem rgba(255,109,0,0.25);
  transition: transform 0.15s, box-shadow 0.15s;
}
.btn-main-start:hover  { transform: translateY(-0.125rem); box-shadow: 0 0.75rem 1.5rem rgba(255,109,0,0.35); }
.btn-main-start:active { transform: translateY(0.0625rem); box-shadow: 0 0.25rem 0.75rem rgba(255,109,0,0.25); }
.arrow { display: inline-block; transition: transform 0.2s; }
.btn-main-start:hover .arrow { transform: translateX(0.25rem); }
</style>
