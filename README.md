<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>nekotron — Scroll Animations</title>
<style>
  *{box-sizing:border-box;margin:0;padding:0}
  html,body{height:100%}
  body{
    font-family:Arial, Helvetica, sans-serif;
    /* NOTE: background sengaja dibiarkan sesuai requestmu */
    background-image: url('file:///C:/Users/raziq/Downloads/266px-Shiroko_(Swimsuit).webp');
    background-size: cover;
    background-position: center;
    background-repeat: no-repeat;
    min-height: 400vh; /* cukup panjang supaya bisa scroll dan terlihat efek */
    color: #fff;
  }

  /* overlay hitam yang akan tambah opacity saat scroll */
  .overlay{
    position:fixed;
    inset:0;
    background-color: rgba(0,0,0,0);
    pointer-events:none;
    z-index: 5;
    transition: background-color 0.15s linear;
  }

  /* HERO / teks utama */
  .hero{
    height:100vh;
    display:flex;
    align-items:center;
    justify-content:center;
    position:relative;
    z-index: 6; /* di atas overlay */
  }
  .hero-text{
    background: rgba(0,0,0,0.36);
    padding:12px 18px;
    border-radius:10px;
    font-size: clamp(1.6rem, 4vw, 3.2rem);
    transition: transform 0.35s ease-out, opacity 0.35s ease-out;
    will-change: transform, opacity;
  }

  /* gallery gambar — berada di bawah hero */
  .gallery{
    max-width:1100px;
    margin: 80px auto 200px;
    padding: 0 20px;
    display:grid;
    grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
    gap: 28px;
    z-index: 6;
    position: relative;
  }
  .gallery img{
    width:100%;
    height:auto;
    display:block;
    border-radius:12px;
    box-shadow: 0 10px 30px rgba(0,0,0,0.45);
    opacity:0;
    transform: translateY(40px);
    transition: opacity 0.75s cubic-bezier(.2,.9,.2,1), transform 0.75s cubic-bezier(.2,.9,.2,1);
    will-change: opacity, transform;
  }
  .gallery img.show{
    opacity:1;
    transform: translateY(0);
  }

  /* opsional: teks footer atau instruksi */
  .note{
    max-width:900px;
    margin: 20px auto 80px;
    color:#ddd;
    text-align:center;
    font-size:0.95rem;
  }
</style>
</head>
<body>

  <div class="overlay" id="overlay"></div>

  <section class="hero" id="hero">
    <h1 class="hero-text" id="heroText">nekotron</h1>
  </section>

  <section class="gallery" id="gallery">
    <!-- Pastikan kamu menyimpan gambar-gambar ini di folder project 'images/' 
         atau ganti src sesuai lokasi filemu -->
    <img src="https://ih1.redbubble.net/image.5441901047.3924/bg,f8f8f8-flat,750x,075,f-pad,750x1000,f8f8f8.jpg" alt="Gambar 1">
    <img src="https://media.tenor.com/ieMgOWd16_oAAAAe/blue-archive-hoshino.png" alt="Gambar 2">
    <img src="https://media.tenor.com/TTymJZMDdroAAAAe/blue-archive-hoshino.png" alt="Gambar 3">
    <img src="https://i.pinimg.com/736x/06/a3/42/06a342551f7b0d4e433370828b658ccd.jpg" alt="Gambar 4">
  </section>

  <div class="note">
    Tip: Jika background file:/// tidak muncul di Live Server, buka file HTML langsung (double-click) atau pindahkan file background ke folder project lalu ubah path ke 'images/shiroko.webp'.
  </div>

<script>
  // Overlay & hero text animation berdasarkan scroll
  const overlay = document.getElementById('overlay');
  const heroText = document.getElementById('heroText');

  window.addEventListener('scroll', () => {
    const scrollY = window.scrollY;
    const winH = window.innerHeight;

    // progress 0..1 relatif terhadap tinggi viewport (hero)
    const p = Math.min(Math.max(scrollY / winH, 0), 1);

    // overlay makin pekat => background "memudar jadi hitam"
    overlay.style.backgroundColor = `rgba(0,0,0,${p})`;

    // teks naik & fade (smooth)
    heroText.style.transform = `translateY(${-p * 60}px)`; // bergerak ke atas
    heroText.style.opacity = `${Math.max(1 - p * 1.5, 0)}`; // jadi transparan lebih cepat
  });

  // Reveal gambar berurutan saat muncul: IntersectionObserver + stagger
  const imgs = document.querySelectorAll('.gallery img');
  const imgsArray = Array.from(imgs);

  const io = new IntersectionObserver((entries) => {
    entries.forEach(entry => {
      if (!entry.isIntersecting) return;
      const img = entry.target;
      const idx = imgsArray.indexOf(img);
      // stagger per gambar (index * delay)
      setTimeout(() => {
        img.classList.add('show');
      }, idx * 250); // 250ms jeda antar gambar
      io.unobserve(img);
    });
  }, {
    root: null,
    rootMargin: '0px 0px -10% 0px',
    threshold: 0.12
  });

  imgs.forEach(i => io.observe(i));
</script>

</body>
</html>

