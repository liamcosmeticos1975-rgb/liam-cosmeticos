<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8"><meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>LIAM Cosméticos Veganos</title>
<style>
body{font-family:system-ui;background:#fdfbf2;margin:0;padding:12px;color:#1a2e1a}
h1{text-align:center;color:#1a4d2e;margin:10px 0 0 0;font-size:36px}
.sub{text-align:center;color:#2e7d32;margin-bottom:12px}
.dep{display:flex;gap:10px;position:sticky;top:0;background:#fdfbf2;padding:10px 0;z-index:10}
.dep button{flex:1;padding:14px;border-radius:14px;border:2px solid #1a4d2e;font-weight:bold;font-size:16px}
.activo{background:#1a4d2e;color:white}.no-activo{background:white;color:#1a4d2e}
.cat{background:#dcedc8;padding:8px 14px;border-radius:20px;margin:20px 0 10px 0;font-weight:bold;color:#1a4d2e;border-left:6px solid #2e7d32}
.prod{background:white;border-radius:16px;padding:14px;margin-bottom:12px;display:flex;justify-content:space-between;align-items:center;border:1px solid #e0e0e0}
.info b{color:#1a4d2e}.price{font-weight:bold;font-size:18px}
.badges{display:flex;gap:6px;margin-top:6px;flex-wrap:wrap}
.badge{font-size:11px;padding:3px 8px;border-radius:10px}.ok{background:#e8f5e9;color:#2e7d32}.no{background:#ffebee;color:#c62828}
.btn{padding:10px 16px;border:none;border-radius:12px;font-weight:bold;background:#1a4d2e;color:white}
.btn:disabled{background:#bdbdbd}
#carrito{position:fixed;bottom:0;left:0;right:0;background:white;border-top:2px solid #1a4d2e;padding:12px;box-shadow:0 -4px 20px rgba(0,0,0,.15)}
</style>
</head>
<body>
<h1>LIAM</h1><div class="sub">Cosméticos Veganos • Chamical - La Rioja</div>

<div class="dep">
  <button id="b-rioja" class="activo" onclick="setDep('rioja')">📦 Stock La Rioja</button>
  <button id="b-bsas" class="no-activo" onclick="setDep('bsas')">📦 Stock Bs As (Moreno)</button>
</div>

<div id="lista"></div>
<div style="height:120px"></div>
<div id="carrito"><b>🛒 Tu pedido (<span id="cant">0</span>)</b><br><button class="btn" style="width:100%;margin-top:8px;background:#25D366" onclick="enviarWsp()">Enviar pedido por WhatsApp</button></div>

<script>
// ===== EDITÁ TU STOCK ACÁ =====
const STOCK = {
  rioja: {
    "crema20_calendula": 10, "crema20_palan": 10, "crema20_manzanilla": 10, "crema20_jarilla": 10, "crema20_jarillamentol": 5, "crema20_rosa": 5,
    "crema30_calendula": 10, "crema30_palan": 10, "crema30_manzanilla": 10, "crema30_jarilla": 10, "crema30_jarillamentol": 5, "crema30_rosa": 5,
    "crema50_calendula": 10, "crema50_palan": 10, "crema50_manzanilla": 10, "crema50_jarilla": 10, "crema50_jarillamentol": 5, "crema50_rosa": 5,
    "aceite15": 20, "aceite30": 20, "aceite45": 20,
    "jabon40_manzanilla": 10, "jabon40_calendula": 10, "jabon40_jarilla": 10, "jabon40_cafe": 10, "jabon40_avena": 10, "jabon40_rosa": 10,
    "jabon60_cafe": 10, "jabon60_jarilla": 10, "jabon60_avena": 10, "jabon60_manzanilla": 10, "jabon60_calendula": 10, "jabon60_rosa": 10, "jabon60_carbon": 10,
    "jabon90_avena": 10, "jabon90_rosa": 10, "jabon90_cafe": 10, "jabon90_jarillaesponja": 10, "jabon90_carbon": 10
  },
  bsas: {
    "crema20_calendula": 2, "crema20_palan": 0, "crema20_manzanilla": 3, "crema20_jarilla": 1, "crema20_jarillamentol": 0, "crema20_rosa": 2,
    "crema30_calendula": 2, "crema30_palan": 1, "crema30_manzanilla": 2, "crema30_jarilla": 2, "crema30_jarillamentol": 0, "crema30_rosa": 1,
    "crema50_calendula": 3, "crema50_palan": 2, "crema50_manzanilla": 3, "crema50_jarilla": 5, "crema50_jarillamentol": 2, "crema50_rosa": 0,
    "aceite15": 5, "aceite30": 3, "aceite45": 2,
    "jabon40_manzanilla": 4, "jabon40_calendula": 2, "jabon40_jarilla": 3, "jabon40_cafe": 0, "jabon40_avena": 1, "jabon40_rosa": 2,
    "jabon60_cafe": 3, "jabon60_jarilla": 2, "jabon60_avena": 2, "jabon60_manzanilla": 2, "jabon60_calendula": 1, "jabon60_rosa": 0, "jabon60_carbon": 1,
    "jabon90_avena": 2, "jabon90_rosa": 1, "jabon90_cafe": 2, "jabon90_jarillaesponja": 0, "jabon90_carbon": 1
  }
};

let depActual = 'rioja';
let carrito = [];

const productos = [
  {id:"crema20_calendula", cat:"CREMAS 20gr", nombre:"Caléndula 20gr", precio:5000},
  {id:"crema20_palan", cat:"CREMAS 20gr", nombre:"Palán Palán 20gr", precio:5000},
  {id:"crema20_manzanilla", cat:"CREMAS 20gr", nombre:"Manzanilla 20gr", precio:5000},
  {id:"crema20_jarilla", cat:"CREMAS 20gr", nombre:"Jarilla 20gr", precio:5000},
  {id:"crema20_jarillamentol", cat:"CREMAS 20gr", nombre:"Jarilla + Mentol 20gr", precio:6000},
  {id:"crema20_rosa", cat:"CREMAS 20gr", nombre:"Rosa Mosqueta 20gr", precio:6000},
  {id:"crema30_calendula", cat:"CREMAS 30gr", nombre:"Caléndula 30gr", precio:6000},
  {id:"crema30_palan", cat:"CREMAS 30gr", nombre:"Palán Palán 30gr", precio:6000},
  {id:"crema30_manzanilla", cat:"CREMAS 30gr", nombre:"Manzanilla 30gr", precio:6000},
  {id:"crema30_jarilla", cat:"CREMAS 30gr", nombre:"Jarilla 30gr", precio:6000},
  {id:"crema30_jarillamentol", cat:"CREMAS 30gr", nombre:"Jarilla + Mentol 30gr", precio:9000},
  {id:"crema30_rosa", cat:"CREMAS 30gr", nombre:"Rosa Mosqueta 30gr", precio:9000},
  {id:"crema50_calendula", cat:"CREMAS 50gr", nombre:"Caléndula 50gr", precio:8000},
  {id:"crema50_palan", cat:"CREMAS 50gr", nombre:"Palán Palán 50gr", precio:8000},
