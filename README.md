<!DOCTYPE html>
<html lang="tr">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Çıkma Teklifi 😊</title>
  <script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.5.1/dist/confetti.browser.min.js"></script>
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
  <style>
    body {
      font-family: Arial, sans-serif;
      background: linear-gradient(to right, #ff9a9e, #fecfef);
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      margin: 0;
      overflow: hidden;
    }

    .container {
      text-align: center;
      background: white;
      padding: 40px;
      border-radius: 20px;
      box-shadow: 0 4px 20px rgba(0,0,0,0.1);
      max-width: 400px;
      position: relative;
    }

    h1 {
      color: #ff6b6b;
      margin-bottom: 10px;
    }

    p {
      color: #666;
      margin-bottom: 30px;
    }

    .btn {
      padding: 12px 24px;
      margin: 10px;
      border: none;
      border-radius: 10px;
      font-size: 16px;
      cursor: pointer;
      transition: all 0.3s ease;
      font-weight: bold;
    }

    .evet { background: #ff6b6b; color: white; }
    .evet:hover { background: #ff5252; transform: scale(1.05); }
    .hayir { background: #4ecdc4; color: white; }
    .hayir:hover { background: #45b7aa; }

    .sonuc { margin-top: 20px; font-size: 18px; color: #333; min-height: 20px; }

    .ring-box-container {
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      display: none;
      z-index: 10;
    }

    .ring-box {
      width: 120px;
      height: 80px;
      background: #333;
      border-radius: 10px;
      position: relative;
      box-shadow: 0 4px 10px rgba(0,0,0,0.3);
    }

    .ring-box-lid {
      width: 100%;
      height: 50%;
      background: #222;
      position: absolute;
      top: 0;
      border-radius: 10px 10px 0 0;
      transform-origin: top;
      transition: transform 1s ease 1s;
    }

    .ring-box.open .ring-box-lid { transform: rotateX(-110deg); }

    .tek-tas-ring {
      width: 60px;
      height: 60px;
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%) scale(0);
      transition: transform 1s ease 2s;
      z-index: 5;
      object-fit: contain;
    }

    .ring-box.open .tek-tas-ring { transform: translate(-50%, -50%) scale(1); }

    .footer {
      position: absolute;
      bottom: 10px;
      left: 50%;
      transform: translateX(-50%);
      font-size: 14px;
      color: #666;
      text-align: center;
    }

    .footer a {
      color: #ff6b6b;
      text-decoration: none;
      display: flex;
      align-items: center;
      justify-content: center;
      gap: 5px;
    }

    .footer a:hover { color: #ff5252; }
    .footer .fa-instagram { font-size: 18px; }

    @keyframes celebrate {
      0% { transform: rotate(0deg) scale(1); }
      100% { transform: rotate(360deg) scale(1.1); }
    }

    .celebrate { animation: celebrate 0.5s ease-in-out; }

    /* Beğeni ve Paylaş düğmeleri */
    .fab-btn {
      position: fixed;
      bottom: 18px;
      padding: 10px 14px;
      border-radius: 28px;
      box-shadow: 0 6px 18px rgba(0,0,0,0.12);
      font-weight: bold;
      display: flex;
      align-items: center;
      gap: 8px;
      cursor: pointer;
      z-index: 8;
      user-select: none;
    }
    #likeWrapper { left: 18px; }
    #likeBtn { background: white; border: 2px solid #ff6b6b; color: #ff6b6b; }
    #likeBtn.liked { background: #ff6b6b; color: white; }
    #shareWrapper { right: 18px; }
    #shareBtn { background: white; border: 2px solid #4ecdc4; color: #4ecdc4; }
    #likeCount { min-width: 30px; text-align: center; font-weight: 700; }
  </style>
</head>
<body>
  <div class="container">
    <h1>Hey, seninle çıkmak istiyorum! 💕</h1>
    <p>Ne dersin?😅</p>
    <button id="evet" class="btn evet">Evet! ❤️</button>
    <button id="hayir" class="btn hayir">Hayır... 😔</button>
    <div id="sonuc" class="sonuc"></div>
    <div class="ring-box-container" id="ringBoxContainer">
      <div class="ring-box" id="ringBox">
        <div class="ring-box-lid"></div>
        <img src="yüzük.png" alt="Tek Taş Yüzük" class="tek-tas-ring">
      </div>
    </div>
    <div class="footer">
      <a href="https://www.instagram.com/xomertursak_/" target="_blank">
        <i class="fab fa-instagram"></i> xomertursak_ Tarafından Yapıldı
      </a>
    </div>
  </div>

  <!-- Beğeni ve Paylaş düğmeleri (kutunun dışında) -->
  <div id="likeWrapper" class="fab-btn" style="left:18px;">
    <button id="likeBtn" aria-pressed="false">
      <i class="fa fa-heart"></i> <span id="likeCount">0</span>
    </button>
  </div>

  <div id="shareWrapper" class="fab-btn" style="right:18px;">
    <button id="shareBtn">
      <i class="fa fa-share-alt"></i> Paylaş
    </button>
  </div>

  <script>
    document.addEventListener('DOMContentLoaded', function() {
      const evetBtn = document.getElementById('evet');
      const hayirBtn = document.getElementById('hayir');
      const sonucDiv = document.getElementById('sonuc');
      const container = document.querySelector('.container');
      const ringBoxContainer = document.getElementById('ringBoxContainer');
      const ringBox = document.getElementById('ringBox');

      const kacmaMesajlari = [
        'Bak, buton bile kaçıyor! 😅 Hadi, Evet de!',
        'Hayır deme, Netflix gecesi yaparız! 🍿',
        'Evet dersen, kahve benden! ☕',
        'Buton sabit, ama kalbim seninle atıyor! 😍',
        'Hadi ama, bu aşk Survivor gibi epik olur! 🏝️'
      ];

      evetBtn.addEventListener('click', function() {
        sonucDiv.innerHTML = '';
        evetBtn.style.display = 'none';
        hayirBtn.style.display = 'none';
        ringBoxContainer.style.display = 'block';

        setTimeout(() => {
          ringBox.classList.add('open');
          container.classList.add('celebrate');
          confetti({ particleCount: 100, spread: 70, origin: { y: 0.6 } });
          setTimeout(() => {
            sonucDiv.innerHTML = 'Harika seçim! bu yüzük senin için 💍';
            sonucDiv.style.color = 'green';
          }, 1500);
        }, 1000);
      });

      hayirBtn.addEventListener('click', function() {
        const randomMesaj = kacmaMesajlari[Math.floor(Math.random() * kacmaMesajlari.length)];
        sonucDiv.innerHTML = randomMesaj;
        sonucDiv.style.color = 'red';
      });
    });
  </script>

  <!-- Firebase bağlantısı ve beğeni-paylaş işlemleri -->
  <script type="module">
    import { initializeApp } from "https://www.gstatic.com/firebasejs/9.22.2/firebase-app.js";
    import { getDatabase, ref, onValue, runTransaction } from "https://www.gstatic.com/firebasejs/9.22.2/firebase-database.js";

    const firebaseConfig = {
      apiKey: "AIzaSyCspl5V-I1fksIevdRpeS8QVx2ueR6Qdd4",
      authDomain: "cikma-teklifi-6ccec.firebaseapp.com",
      databaseURL: "https://cikma-teklifi-6ccec-default-rtdb.firebaseio.com",
      projectId: "cikma-teklifi-6ccec",
      storageBucket: "cikma-teklifi-6ccec.firebasestorage.app",
      messagingSenderId: "889184866731",
      appId: "1:889184866731:web:81ed03ad085d480daa82ee",
      measurementId: "G-Q55WNX9ZVV"
    };

    const app = initializeApp(firebaseConfig);
    const db = getDatabase(app);
    const likesRef = ref(db, 'likes/count');

    const likeBtn = document.getElementById('likeBtn');
    const likeCountEl = document.getElementById('likeCount');
    const shareBtn = document.getElementById('shareBtn');
    const LOCAL_KEY = 'ct_like_v1';
    let liked = localStorage.getItem(LOCAL_KEY) === '1';

    onValue(likesRef, (snap) => {
      const val = snap.exists() ? snap.val() : 0;
      likeCountEl.textContent = val;
    });

    updateLikeUI();

    likeBtn.addEventListener('click', async () => {
      likeBtn.disabled = true;
      try {
        if (liked) {
          await runTransaction(likesRef, (current) => Math.max((current || 0) - 1, 0));
          liked = false;
          localStorage.setItem(LOCAL_KEY, '0');
        } else {
          await runTransaction(likesRef, (current) => (current || 0) + 1);
          liked = true;
          localStorage.setItem(LOCAL_KEY, '1');
        }
        updateLikeUI();
      } catch (err) {
        console.error('Like transaction failed', err);
      } finally {
        likeBtn.disabled = false;
      }
    });

    function updateLikeUI() {
      if (liked) {
        likeBtn.classList.add('liked');
      } else {
        likeBtn.classList.remove('liked');
      }
    }

    shareBtn.addEventListener('click', async () => {
      const shareData = { title: document.title, text: 'Bak şuna ❤️', url: location.href };
      if (navigator.share) {
        try { await navigator.share(shareData); } 
        catch (err) { console.log('Paylaşım iptal', err); }
      } else {
        try {
          await navigator.clipboard.writeText(location.href);
          alert('Bağlantı kopyalandı! 📋');
        } catch {
          prompt('Kopyala ve paylaş:', location.href);
        }
      }
    });
  </script>
</body>
</html>
