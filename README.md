<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AHMET ANALIK SUNAR: PC Tycoon Global</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        :root { --ana: #0a0f1e; --kart: #161e35; --vurgu: #8b5cf6; --basari: #10b981; --uyari: #f59e0b; --hata: #ef4444; --pembis: #ec4899; }
        body { background: var(--ana); color: white; font-family: 'Inter', sans-serif; margin: 0; overflow-x: hidden; }

        /* INTRO */
        #intro-ekrani { 
            position: fixed; top: 0; left: 0; width: 100%; height: 100%; 
            background: #000; z-index: 10000; display: flex; justify-content: center; align-items: center; text-align: center;
        }
        .intro-text { 
            font-size: 2.5rem; font-weight: 900; letter-spacing: 5px;
            background: linear-gradient(90deg, #fff, var(--vurgu), #fff);
            background-size: 200%; -webkit-background-clip: text; -webkit-text-fill-color: transparent;
            animation: shine 3s linear infinite;
        }
        @keyframes shine { to { background-position: 200%; } }

        /* MENÜ */
        #ana-menu {
            position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: radial-gradient(circle at center, #1e293b 0%, #0a0f1e 100%);
            z-index: 9000; display: none; flex-direction: column; justify-content: center; align-items: center;
        }
        .menu-btn {
            background: rgba(139, 92, 246, 0.1); border: 2px solid var(--vurgu);
            color: white; padding: 15px 40px; border-radius: 50px; font-size: 1.1rem;
            margin: 10px; cursor: pointer; transition: 0.3s; width: 260px; text-align: center;
        }
        .menu-btn:hover { background: var(--vurgu); box-shadow: 0 0 20px var(--vurgu); }
        .insta-link { position: absolute; bottom: 30px; color: #94a3b8; text-decoration: none; font-size: 0.9rem; }

        /* OYUN PANELİ */
        #oyun-arayuzu { display: none; padding-bottom: 80px; }
        #ust-panel { 
            background: rgba(22, 30, 53, 0.95); padding: 12px; display: grid; 
            grid-template-columns: repeat(4, 1fr); border-bottom: 2px solid var(--vurgu); 
            position: sticky; top: 0; z-index: 8000; text-align: center; 
        }
        .stat-val { font-weight: 800; display: block; }
        .stat-label { font-size: 0.6rem; color: #94a3b8; }

        /* EKRANLAR */
        .ekran { display: none; padding: 15px; }
        .aktif { display: block; }
        .grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(140px, 1fr)); gap: 12px; }
        .kart { background: var(--kart); border-radius: 12px; padding: 15px; text-align: center; border: 1px solid rgba(255,255,255,0.05); }

        /* MARKET MODAL */
        #market-modal { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: var(--ana); z-index: 9500; display: none; padding: 20px; overflow-y: auto; }
        .parca-liste-kart { background: var(--kart); margin-bottom: 12px; padding: 12px; border-radius: 10px; display: flex; align-items: center; gap: 15px; border: 1px solid #334155; }
        .parca-liste-kart img { width: 60px; height: 60px; border-radius: 8px; background: #fff; object-fit: contain; }

        /* YORUMLAR & GÖREVLER */
        .yorum-kart { background: #1e293b; padding: 10px; border-radius: 8px; margin-top: 8px; border-left: 3px solid var(--basari); font-size: 0.8rem; }
        .gorev-kart { background: #161e35; padding: 15px; border-radius: 12px; margin-bottom: 15px; border: 1px solid var(--vurgu); }
        .parca-check { font-size: 0.7rem; color: #64748b; margin-right: 5px; }
        .check-ok { color: var(--basari); font-weight: bold; }

        nav { background: #0f172a; position: fixed; bottom: 0; width: 100%; display: flex; justify-content: space-around; padding: 12px 0; border-top: 1px solid #334155; }
        nav button { background: none; border: none; color: #64748b; display: flex; flex-direction: column; align-items: center; font-size: 0.7rem; }
        nav button.aktif-nav { color: var(--vurgu); }
        .btn { background: var(--vurgu); color: white; border: none; padding: 10px; border-radius: 6px; cursor: pointer; font-weight: bold; }
        .fiyat { color: var(--basari); font-weight: bold; }
    </style>
</head>
<body>

<div id="intro-ekrani"><div class="intro-text">AHMET ANALIK<br>SUNAR</div></div>

<div id="ana-menu">
    <h1 style="text-shadow: 0 0 15px var(--vurgu);">PC TYCOON</h1>
    <div class="menu-btn" onclick="oyunuBaslat()">OYUNA BAŞLA</div>
    <div class="menu-btn" id="gunluk-odul-btn" onclick="gunlukOdulAl()">GÜNLÜK ÖDÜL</div>
    <a href="https://instagram.com/ahmet_analk311" class="insta-link"><i class="fab fa-instagram"></i> ahmet_analk311</a>
</div>

<div id="market-modal">
    <button class="btn" style="background:var(--hata); margin-bottom:20px" onclick="kapatMarket()">← Geri</button>
    <h2 id="modal-baslik">Kategori</h2>
    <div id="modal-liste"></div>
</div>

<div id="oyun-arayuzu">
    <div id="ust-panel">
        <div class="stat-item"><span class="stat-label">BAKİYE</span><span class="stat-val" id="para">10000</span></div>
        <div class="stat-item"><span class="stat-label">İTİBAR</span><span class="stat-val" id="itibar">%100</span></div>
        <div class="stat-item"><span class="stat-label">SAAT</span><span class="stat-val" id="saat">09:00</span></div>
        <div class="stat-item"><span class="stat-label">STOK</span><span class="stat-val" id="depo-adet">0</span></div>
    </div>

    <div id="tasks" class="ekran aktif">
        <h3><i class="fas fa-microchip"></i> Siparişler</h3>
        <div id="gorev-listesi"></div>
        <h3><i class="fas fa-star"></i> Müşteri Yorumları</h3>
        <div id="yorum-listesi"></div>
    </div>

    <div id="market" class="ekran">
        <h3><i class="fas fa-store"></i> Market</h3>
        <div class="grid" id="market-kategoriler"></div>
    </div>

    <div id="inv" class="ekran">
        <h3><i class="fas fa-box"></i> Depo</h3>
        <div class="grid" id="depo-grid"></div>
    </div>

    <nav>
        <button onclick="ekranDegistir('tasks', this)" class="aktif-nav"><i class="fas fa-home"></i>Atölye</button>
        <button onclick="ekranDegistir('market', this)"><i class="fas fa-shopping-cart"></i>Market</button>
        <button onclick="ekranDegistir('inv', this)"><i class="fas fa-warehouse"></i>Depo</button>
    </nav>
</div>

<script>
let state = { para: 10000, itibar: 100, saat: 9, dk: 0, depo: [], gorevler: [], yorumlar: [], odul: false };

const parcaKatalogu = {
    'CPU': [{ ad: 'i3-12100F', fiyat: 2500, resim: 'https://m.media-amazon.com/images/I/51Hk+nID02L.jpg' }, { ad: 'i9-14900K', fiyat: 19000, resim: 'https://m.media-amazon.com/images/I/61b7L4xN9uL.jpg' }],
    'GPU': [{ ad: 'GTX 1650', fiyat: 4200, resim: 'https://m.media-amazon.com/images/I/71it8rL9E2L.jpg' }, { ad: 'RTX 4090', fiyat: 80000, resim: 'https://m.media-amazon.com/images/I/81xUeO2yPXL.jpg' }],
    'RAM': [{ ad: '8GB DDR4', fiyat: 800, resim: 'https://m.media-amazon.com/images/I/61vYf6x6V9L.jpg' }, { ad: '32GB RGB', fiyat: 4500, resim: 'https://m.media-amazon.com/images/I/61IFr6-zNLL.jpg' }],
    'CASE': [{ ad: 'Ofis Kasa', fiyat: 700, resim: 'https://m.media-amazon.com/images/I/61SjD6m826L.jpg' }, { ad: 'RGB Kasa', fiyat: 2500, resim: 'https://m.media-amazon.com/images/I/71YyP+fUunL.jpg' }],
    'MB': [{ ad: 'H610M', fiyat: 2400, resim: 'https://m.media-amazon.com/images/I/81Pj6pGqVML.jpg' }],
    'PSU': [{ ad: '500W', fiyat: 1100, resim: 'https://m.media-amazon.com/images/I/71BfVp68u8L.jpg' }],
    'FAN': [{ ad: 'Stok Fan', fiyat: 200, resim: 'https://m.media-amazon.com/images/I/51mXm5S3oSL.jpg' }]
};

const zorunlu = ['CPU', 'GPU', 'RAM', 'CASE', 'MB', 'PSU', 'FAN'];

// INTRO & MENÜ
setTimeout(() => {
    document.getElementById('intro-ekrani').style.display = 'none';
    document.getElementById('ana-menu').style.display = 'flex';
}, 3500);

function oyunuBaslat() {
    document.getElementById('ana-menu').style.display = 'none';
    document.getElementById('oyun-arayuzu').style.display = 'block';
    renderMarketKategoriler();
    gorevEkle();
}

function gunlukOdulAl() {
    if(!state.odul) {
        state.para += 5000; state.odul = true;
        document.getElementById('para').innerText = state.para;
        alert("5.000 TL ödül alındı!");
    }
}

// MARKET
function renderMarketKategoriler() {
    document.getElementById('market-kategoriler').innerHTML = zorunlu.map(k => `
        <div class="kart" onclick="marketAc('${k}')"><i class="fas fa-box"></i><br>${k}</div>
    `).join('');
}

function marketAc(kat) {
    document.getElementById('market-modal').style.display = 'block';
    document.getElementById('modal-baslik').innerText = kat;
    document.getElementById('modal-liste').innerHTML = parcaKatalogu[kat].map(p => `
        <div class="parca-liste-kart">
            <img src="${p.resim}">
            <div style="flex-grow:1"><b>${p.ad}</b><br><span class="fiyat">${p.fiyat} TL</span></div>
            <button class="btn" onclick="satinal('${kat}','${p.ad}',${p.fiyat})">Al</button>
        </div>
    `).join('');
}

function satinal(tip, ad, fiyat) {
    if(state.para >= fiyat) {
        state.para -= fiyat; state.depo.push({tip, ad, uid:Math.random()});
        arayuzGuncelle(); renderGorevler(); alert("Alındı!");
    }
}

// ATÖLYE
function gorevEkle() {
    if(state.gorevler.length < 2) {
        state.gorevler.push({ id: Date.now(), odul: 45000 });
        renderGorevler();
    }
}

function renderGorevler() {
    document.getElementById('gorev-listesi').innerHTML = state.gorevler.map(g => {
        let list = zorunlu.map(t => `<span class="parca-check ${state.depo.some(p=>p.tip===t)?'check-ok':''}">- ${t}</span>`).join('');
        let ok = zorunlu.every(t => state.depo.some(p => p.tip === t));
        return `<div class="gorev-kart"><b>Sistem Montajı</b><br>${list}<br><button class="btn" ${ok?'':'disabled'} onclick="teslim(${g.id})">Teslim Et (${g.odul} TL)</button></div>`;
    }).join('');
}

function teslim(id) {
    zorunlu.forEach(t => state.depo.splice(state.depo.findIndex(p=>p.tip===t), 1));
    state.para += 45000; state.gorevler = state.gorevler.filter(x=>x.id!==id);
    state.yorumlar.unshift({ n:"Gamer", m:"Harika sistem!", s:5 });
    renderYorumlar(); renderGorevler(); arayuzGuncelle();
}

function renderYorumlar() {
    document.getElementById('yorum-listesi').innerHTML = state.yorumlar.slice(0,3).map(y => `<div class="yorum-kart"><b>${y.n}</b>: ${y.m}</div>`).join('');
}

function arayuzGuncelle() {
    document.getElementById('para').innerText = Math.floor(state.para);
    document.getElementById('depo-adet').innerText = state.depo.length;
}

function ekranDegistir(id, btn) {
    document.querySelectorAll('.ekran').forEach(e => e.classList.remove('aktif'));
    document.getElementById(id).classList.add('aktif');
}
function kapatMarket() { document.getElementById('market-modal').style.display = 'none'; }
setInterval(() => { state.dk += 10; if(state.dk>=60){state.dk=0; state.saat++; gorevEkle();} document.getElementById('saat').innerText = state.saat+":00"; }, 3000);
</script>
</body>
</html>
