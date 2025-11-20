<!doctype html>
<html lang="es">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Cafetería UdeC — App Demo</title>

<!-- QR lib -->
<script src="https://cdn.jsdelivr.net/npm/qrcode@1.5.1/build/qrcode.min.js"></script>

<style>
  :root{
    --udec-green:#0f8a3f;
    --udec-yellow:#ffd100;
    --bg:#f5f7f4;
    --card-radius:14px;
    --glass: rgba(255,255,255,0.85);
  }
  *{box-sizing:border-box}
  body{
    margin:0;
    font-family: Inter, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;
    background: linear-gradient(180deg,#f3f8f4 0%, #fefde6 100%);
    color:#092617;
    -webkit-font-smoothing:antialiased;
  }

  /* App shell */
  .app {
    max-width:1100px;
    margin:18px auto;
    padding:18px;
  }

  header.app-header{
    display:flex;
    gap:12px;
    align-items:center;
    padding:12px;
    border-radius:12px;
    background: linear-gradient(90deg, rgba(15,138,63,0.08), rgba(255,209,0,0.06));
    backdrop-filter: blur(4px);
    box-shadow: 0 8px 28px rgba(7,58,29,0.06);
  }
  .logo {
    width:64px; height:64px; border-radius:12px; overflow:hidden; flex-shrink:0;
    box-shadow:0 6px 18px rgba(7,58,29,0.08);
    background: white;
    display:flex; align-items:center; justify-content:center;
  }
  .logo img{width:100%; height:100%; object-fit:cover}
  .brand {
    display:flex; flex-direction:column;
  }
  .brand .title{font-weight:800; color:var(--udec-green); font-size:18px}
  .brand .subtitle{font-size:12px; color:#335942}

  /* top controls */
  .top-controls{margin-left:auto; display:flex; gap:10px; align-items:center}
  .btn { border:0; padding:8px 12px; border-radius:10px; cursor:pointer; font-weight:600; }
  .btn-primary { background: linear-gradient(90deg,var(--udec-green),var(--udec-yellow)); color:white; box-shadow:0 6px 20px rgba(15,138,63,0.12);}
  .btn-ghost { background:transparent; border:1px solid rgba(7,58,29,0.08); color:#234; }

  /* layout */
  .layout { display:grid; grid-template-columns: 1fr 360px; gap:18px; margin-top:18px; align-items:start; }
  @media (max-width:980px){ .layout{grid-template-columns:1fr} .right-col{position:relative; top:0} }

  /* left column: sections */
  .section {
    background: var(--glass);
    border-radius: var(--card-radius);
    padding:14px;
    box-shadow: 0 8px 30px rgba(7,58,29,0.03);
    border:1px solid rgba(7,58,29,0.03);
    overflow:hidden;
  }

  .search-row { display:flex; gap:8px; margin-bottom:12px; align-items:center; }
  .search-row input{
    flex:1; padding:10px 12px; border-radius:10px; border:1px solid rgba(7,58,29,0.08);
    outline:none; font-size:14px;
  }
  .filter-tag { background:#fffbe0; color:#6b4e00; padding:6px 8px; border-radius:999px; font-size:13px; box-shadow:0 4px 12px rgba(0,0,0,0.03); }

  /* category tiles */
  .categories { display:grid; grid-template-columns: repeat(2, minmax(0,1fr)); gap:12px; margin-bottom:12px; }
  .cat {
    background: linear-gradient(135deg, rgba(15,138,63,0.06), rgba(255,209,0,0.04));
    border-radius:12px; padding:10px; cursor:pointer; transition:transform .18s ease, box-shadow .18s ease;
    display:flex; gap:10px; align-items:center;
  }
  .cat:hover{ transform:translateY(-6px); box-shadow:0 18px 40px rgba(15,138,63,0.06); }
  .cat .emoji {font-size:28px}
  .cat .info { display:flex; flex-direction:column; }
  .cat .info .name{ font-weight:700; color:var(--udec-green) }
  .cat .info .count{ font-size:12px; color:#3b5e49 }

  /* product grid */
  .product-grid {
    display:grid;
    grid-template-columns: repeat(auto-fill, minmax(220px,1fr));
    gap:12px;
    margin-top:12px;
  }
  .card {
    background:white; border-radius:12px; padding:10px; display:flex; flex-direction:column; gap:8px;
    transition: transform .18s ease, box-shadow .18s ease;
    border:1px solid rgba(10,40,20,0.03);
  }
  .card:hover { transform: translateY(-8px); box-shadow:0 20px 40px rgba(15,138,63,0.06); }
  .thumb { height:120px; border-radius:10px; overflow:hidden; background:linear-gradient(90deg,#f3fff6,#fffde6); display:flex; align-items:center; justify-content:center; color:var(--udec-green); font-weight:700 }
  .card h4{ margin:0; font-size:16px; color:#12311f }
  .card p{ margin:0; font-size:13px; color:#4a6a57 }
  .card .row { display:flex; justify-content:space-between; align-items:center; gap:8px; margin-top:auto }
  .qty { display:flex; gap:6px; align-items:center }
  .qty input{ width:56px; padding:6px; border-radius:8px; border:1px solid rgba(7,58,29,0.06) }

  /* right column (cart) */
  .right-col { position:sticky; top:18px; align-self:start; }
  .cart {
    background: linear-gradient(180deg,#fff 0%, #fbfff8 100%);
    border-radius:12px; padding:12px; border:1px solid rgba(7,58,29,0.04);
  }
  .cart h3 { margin:0 0 8px 0; color:var(--udec-green) }
  .cart-list { max-height:320px; overflow:auto; display:flex; flex-direction:column; gap:8px; padding-right:8px }
  .cart-item { display:flex; gap:8px; align-items:center; background:#fff; padding:8px; border-radius:10px; border:1px solid rgba(7,58,29,0.03) }
  .cart-item .meta { flex:1 }
  .cart-item .meta .name{ font-weight:700; font-size:14px }
  .cart-item .meta .price{ font-size:13px; color:#3b5e49 }
  .cart-footer { margin-top:10px; display:flex; flex-direction:column; gap:8px }

  .total-row { display:flex; justify-content:space-between; font-weight:800; font-size:20px; color:var(--udec-green) }

  /* orders / mis pedidos */
  .orders {
    margin-top:18px; padding:12px; border-radius:12px; background:linear-gradient(90deg, rgba(15,138,63,0.03), rgba(255,209,0,0.02)); border:1px solid rgba(7,58,29,0.03)
  }
  .orders .hero {
    height:140px; border-radius:10px; overflow:hidden; position:relative; margin-bottom:10px;
  }
  .orders .hero img{ width:100%; height:100%; object-fit:cover; filter:brightness(.88) saturate(.9) }
  .order-card { display:flex; gap:12px; background:white; padding:10px; border-radius:10px; align-items:center; border:1px solid rgba(7,58,29,0.03) }
  .order-card .qr { width:84px; height:84px; display:flex; align-items:center; justify-content:center; }
  .status { font-weight:700; font-size:13px }
  .status.pendiente { color:#b45309 }
  .status.en_preparacion { color:#0ea5b7 }
  .status.listo { color:#059669 }

  /* small animations */
  .fade-in { animation: fadeIn .35s ease both; }
  @keyframes fadeIn { from { opacity:0; transform: translateY(8px) } to { opacity:1; transform:none } }

  /* mobile tweaks */
  @media (max-width:600px){
    .categories{ grid-template-columns:1fr 1fr }
    .product-grid{ grid-template-columns:1fr }
  }
</style>
</head>
<body>
  <div class="app">
    <header class="app-header fade-in">
      <div class="logo"><img src="/mnt/data/130375af-b992-4881-b401-f77ba740bf84.png" alt="Logo UdeC"></div>
      <div class="brand">
        <div class="title">Cafetería UdeC</div>
        <div class="subtitle">Universidad de Colima — Servicio Estudiantil</div>
      </div>

      <div class="top-controls">
        <div class="filter-tag" id="welcomeTag">Bienvenido — Inicia sesión</div>
        <button class="btn btn-ghost" id="btnOrders">Mis Pedidos</button>
        <button class="btn btn-primary" id="openLoginBtn">Iniciar sesión</button>
      </div>
    </header>

    <div class="layout">
      <!-- LEFT: menu -->
      <div>
        <div class="section fade-in">
          <div class="search-row">
            <input id="search" placeholder="Buscar (ej. 'sopa', 'latte', 'ensalada')">
            <button class="btn btn-ghost" id="clearSearch">Limpiar</button>
            <div style="width:12px"></div>
            <div class="filter-tag" id="searchCount">0 resultados</div>
          </div>

          <div class="categories" id="categoryBar">
            <div class="cat" data-cat="bebidas">
              <div class="emoji">☕</div>
              <div class="info">
                <div class="name">Bebidas</div>
                <div class="count" id="count-bebidas">—</div>
              </div>
            </div>
            <div class="cat" data-cat="comidas">
              <div class="emoji">🍽️</div>
              <div class="info">
                <div class="name">Comidas</div>
                <div class="count" id="count-comidas">—</div>
              </div>
            </div>
            <div class="cat" data-cat="snacks">
              <div class="emoji">🍪</div>
              <div class="info">
                <div class="name">Snacks</div>
                <div class="count" id="count-snacks">—</div>
              </div>
            </div>
            <div class="cat" data-cat="postres">
              <div class="emoji">🍰</div>
              <div class="info">
                <div class="name">Postres</div>
                <div class="count" id="count-postres">—</div>
              </div>
            </div>
          </div>

          <!-- tabs for subcategories -->
          <div style="display:flex; gap:8px; margin-top:12px;">
            <button class="btn btn-ghost" id="tabAll">Todos</button>
            <button class="btn btn-ghost" id="tabFrio">Frías</button>
            <button class="btn btn-ghost" id="tabCaliente">Calientes</button>
            <button class="btn btn-ghost" id="tabEntradas">Entradas</button>
          </div>

          <!-- product grid -->
          <div id="grid" class="product-grid" style="margin-top:14px"></div>
        </div>

        <!-- interactive tips -->
        <div class="section fade-in" style="margin-top:12px; display:flex; gap:12px; align-items:center;">
          <div style="flex:1">
            <h4 style="margin:0;color:#234d36">¿Cómo ordenar?</h4>
            <p style="margin:6px 0 0 0; font-size:13px; color:#4a6a57">Inicia sesión → Explora el menú → Añade al carrito → Finaliza pago (tarjeta / efectivo). Los números de pedido se reinician cada día.</p>
          </div>
          <div>
            <button class="btn btn-primary" id="goMenu">Ir al Menú</button>
          </div>
        </div>
      </div>

      <!-- RIGHT: cart & orders -->
      <aside class="right-col">
        <div class="cart fade-in">
          <h3>Tu carrito</h3>
          <div id="cartList" class="cart-list"></div>

          <div class="cart-footer">
            <div class="total-row"><span>Total</span><span id="cartTotal">$0.00</span></div>
            <div style="display:flex; gap:8px;">
              <button class="btn btn-ghost" id="btnClear">Vaciar</button>
              <button class="btn btn-primary" id="btnCheckout">Pagar</button>
            </div>
          </div>
        </div>

        <div class="orders fade-in" id="ordersBox">
          <div class="hero">
            <img src="/mnt/data/f29137ab-3fb7-4743-a3fb-7cfc5567dcc9.png" alt="Cafetería UdeC">
          </div>
          <h4 style="margin:6px 0 8px 0">Mis Pedidos</h4>
          <div id="ordersList" style="display:flex; flex-direction:column; gap:8px"></div>
          <div style="margin-top:8px; font-size:12px; color:#36583f">Imagen: cafetería UdeC — fondo e ilustración.</div>
        </div>
      </aside>
    </div>
  </div>

  <!-- Modals -->
  <div id="modalBack" style="position:fixed; inset:0; display:none; align-items:center; justify-content:center; background:rgba(0,0,0,0.45); z-index:60;">
    <div style="width:92%; max-width:720px; background:white; border-radius:12px; overflow:hidden;">
      <div style="display:flex; gap:12px;">
        <div style="flex:1; padding:18px;">
          <h3 id="modalTitle" style="margin:0 0 8px 0; color:var(--udec-green)"></h3>
          <div id="modalBody"></div>
        </div>
        <div style="width:240px; border-left:1px solid #eef6ec; padding:18px;">
          <div style="display:flex; gap:8px; align-items:center;">
            <div style="width:44px; height:44px; border-radius:10px; overflow:hidden; background:#fff"><img src="/mnt/data/130375af-b992-4881-b401-f77ba740bf84.png" style="width:100%; height:100%; object-fit:cover"></div>
            <div>
              <div style="font-weight:800; color:var(--udec-green)">Cafetería UdeC</div>
              <div style="font-size:12px; color:#4a6a57">Servicio Estudiantil</div>
            </div>
          </div>

          <div id="modalRightFooter" style="margin-top:18px"></div>
        </div>
      </div>
      <div style="padding:12px; display:flex; justify-content:flex-end; gap:8px; border-top:1px solid #f0f6f0">
        <button class="btn btn-ghost" id="modalClose">Cerrar</button>
      </div>
    </div>
  </div>

<script>
/* -----------------------------
   Data: menu ampliado (organizado por apartados)
   ----------------------------- */
const MENU = {
  bebidas: {
    frias: [
      { id:'b-f-01', name:'Iced Latte', price: 48, desc:'Leche + espresso + hielo' },
      { id:'b-f-02', name:'Frappé Chocolate', price: 55, desc:'Frappé con crema' },
      { id:'b-f-03', name:'Iced Matcha', price: 56, desc:'Matcha estilo UdeC' },
      { id:'b-f-04', name:'Limonada con menta', price: 28, desc:'Limonada natural' }
    ],
    calientes: [
      { id:'b-c-01', name:'Capuchino', price: 38, desc:'Espresso + leche espumada' },
      { id:'b-c-02', name:'Americano', price: 30, desc:'Café expreso al agua' },
      { id:'b-c-03', name:'Latte Vainilla', price: 42, desc:'Latte con vainilla' },
      { id:'b-c-04', name:'Chocolate Caliente', price: 36, desc:'Chocolate espeso con leche' }
    ]
  },
  comidas: {
    entradas: [
      { id:'c-e-01', name:'Ensalada César', price:58, desc:'Lechuga, crotones, parmesano' },
      { id:'c-e-02', name:'Sopa del día', price:34, desc:'Sopa casera' },
      { id:'c-e-03', name:'Nachos con queso', price:45, desc:'Nachos + queso fundido' },
      { id:'c-e-04', name:'Rollitos vegetarianos', price:40, desc:'Relleno de verduras' }
    ],
    fuertes: [
      { id:'c-f-01', name:'Bowl integral', price:65, desc:'Proteínas, quinoa y vegetales' },
      { id:'c-f-02', name:'Tacos al pastor (3)', price:48, desc:'Con cebolla y cilantro' },
      { id:'c-f-03', name:'Torta de pierna', price:62, desc:'Torta tradicional' },
      { id:'c-f-04', name:'Chilaquiles verdes', price:72, desc:'Con pollo o huevo' }
    ]
  },
  snacks: {
    sanos: [
      { id:'s-s-01', name:'Barra de granola', price:18, desc:'Avena y frutos secos' },
      { id:'s-s-02', name:'Yogurt con fruta', price:32, desc:'Yogurt natural con toppings' },
      { id:'s-s-03', name:'Fruta picada', price:25, desc:'Selección de fruta fresca' }
    ],
    chatarra: [
      { id:'s-c-01', name:'Galleta casera', price:12, desc:'Galleta de chispas' },
      { id:'s-c-02', name:'Papas fritas', price:22, desc:'Snack clásico' },
      { id:'s-c-03', name:'Brownie', price:28, desc:'Brownie de chocolate' }
    ]
  },
  postres: [
    { id:'p-01', name:'Cheesecake', price:48, desc:'Porción cremosa' },
    { id:'p-02', name:'Flan casero', price:28, desc:'Flan tradicional' }
  ]
};

/* -----------------------------
   State: cart, orders, user
   ----------------------------- */
let cart = JSON.parse(localStorage.getItem('udec_cart_v3') || '[]');
let orders = JSON.parse(localStorage.getItem('udec_orders_v3') || '[]');
let user = JSON.parse(localStorage.getItem('udec_user_v3') || 'null');

const el = (id)=>document.getElementById(id);

/* -----------------------------
   Helpers
   ----------------------------- */
function formatMoney(n){ return '$' + Number(n).toFixed(2); }
function persist(){
  localStorage.setItem('udec_cart_v3', JSON.stringify(cart));
  localStorage.setItem('udec_orders_v3', JSON.stringify(orders));
  localStorage.setItem('udec_user_v3', JSON.stringify(user));
}

/* -----------------------------
   Render counts + grid
   ----------------------------- */
function countAll(){
  const cB = MENU.bebidas.frias.length + MENU.bebidas.calientes.length;
  const cC = MENU.comidas.entradas.length + MENU.comidas.fuertes.length;
  const cS = MENU.snacks.sanos.length + MENU.snacks.chatarra.length;
  const cP = MENU.postres.length;
  el('count-bebidas').textContent = cB + ' items';
  el('count-comidas').textContent = cC + ' items';
  el('count-snacks').textContent = cS + ' items';
  el('count-postres').textContent = cP + ' items';
}
function buildGrid(filter=''){
  const grid = el('grid');
  grid.innerHTML = '';
  const f = (filter || '').trim().toLowerCase();
  const pushCard = (p, catLabel, subLabel)=>{
    if(f){
      if(!(p.name.toLowerCase().includes(f) || (p.desc||'').toLowerCase().includes(f))) return;
    }
    const card = document.createElement('div'); card.className='card';
    const thumb = document.createElement('div'); thumb.className='thumb'; thumb.textContent = catLabel;
    const h = document.createElement('h4'); h.textContent = p.name;
    const d = document.createElement('p'); d.textContent = p.desc || '';
    const row = document.createElement('div'); row.className='row';
    const price = document.createElement('div'); price.style.fontWeight='800'; price.style.color='var(--udec-green)'; price.textContent = formatMoney(p.price);
    const ctr = document.createElement('div'); ctr.className='qty';
    const qty = document.createElement('input'); qty.type='number'; qty.min=1; qty.value=1;
    const add = document.createElement('button'); add.className='btn btn-primary'; add.textContent='Agregar';
    add.onclick = ()=> { addToCart({ ...p, qty: Number(qty.value) }); qty.value=1; };
    ctr.appendChild(qty); ctr.appendChild(add);
    row.appendChild(price); row.appendChild(ctr);
    card.appendChild(thumb); card.appendChild(h); card.appendChild(d); card.appendChild(row);
    grid.appendChild(card);
  };

  // bebidas frias then calientes
  MENU.bebidas.frias.forEach(p=>pushCard(p,'FRÍAS','bebida'));
  MENU.bebidas.calientes.forEach(p=>pushCard(p,'CALIENTES','bebida'));
  // comidas
  MENU.comidas.entradas.forEach(p=>pushCard(p,'ENTRADA','comida'));
  MENU.comidas.fuertes.forEach(p=>pushCard(p,'PLATO','comida'));
  // snacks
  MENU.snacks.sanos.forEach(p=>pushCard(p,'SALUDABLE','snack'));
  MENU.snacks.chatarra.forEach(p=>pushCard(p,'CHATARRA','snack'));
  // postres
  MENU.postres.forEach(p=>pushCard(p,'POSTRE','postre'));

  // update searchCount
  const visible = grid.children.length;
  el('searchCount').textContent = visible + ' resultados';
}

/* -----------------------------
   Cart functions
   ----------------------------- */
function addToCart(product){
  const existing = cart.find(x=>x.id === product.id);
  if(existing) existing.qty += product.qty;
  else cart.push({ id:product.id, name:product.name, price:product.price, qty:product.qty });
  persist(); renderCart();
  toast('Agregado: ' + product.name);
}
function renderCart(){
  const list = el('cartList');
  list.innerHTML = '';
  if(cart.length === 0){ list.innerHTML = '<div style="color:#4a6a57">Tu carrito está vacío</div>'; el('cartTotal').textContent = formatMoney(0); return; }
  cart.forEach((it, idx)=>{
    const row = document.createElement('div'); row.className='cart-item';
    const meta = document.createElement('div'); meta.className='meta';
    const name = document.createElement('div'); name.className='name'; name.textContent = it.name;
    const price = document.createElement('div'); price.className='price'; price.textContent = formatMoney(it.price) + ' × ' + it.qty;
    meta.appendChild(name); meta.appendChild(price);
    const controls = document.createElement('div');
    const plus = document.createElement('button'); plus.className='btn btn-ghost'; plus.textContent='+'; plus.onclick=()=>{ it.qty+=1; persist(); renderCart(); };
    const minus = document.createElement('button'); minus.className='btn btn-ghost'; minus.textContent='-'; minus.onclick=()=>{ it.qty = Math.max(1, it.qty-1); persist(); renderCart(); };
    const del = document.createElement('button'); del.className='btn'; del.textContent='🗑'; del.onclick=()=>{ cart.splice(idx,1); persist(); renderCart(); };
    controls.appendChild(plus); controls.appendChild(minus); controls.appendChild(del);
    row.appendChild(meta); row.appendChild(controls);
    list.appendChild(row);
  });
  const total = cart.reduce((s,i)=>s + i.price * i.qty, 0);
  el('cartTotal').textContent = formatMoney(total);
}

/* -----------------------------
   Orders: daily numeric counter
   ----------------------------- */
function getNextDailyNumber(){
  const key = 'udec_order_seq_v3';
  const today = new Date().toISOString().slice(0,10);
  const stored = JSON.parse(localStorage.getItem(key) || '{}');
  if(stored.date !== today){
    stored.date = today; stored.counter = 1;
  } else {
    stored.counter = (stored.counter || 0) + 1;
  }
  localStorage.setItem(key, JSON.stringify(stored));
  // pad to 4 digits like 0001
  return String(stored.counter).padStart(4,'0');
}
function createOrder(paymentMethod, customerName, cardLast4){
  if(cart.length === 0) { alert('El carrito está vacío'); return; }
  const orderNumber = getNextDailyNumber();
  const newOrder = {
    id: 'o-' + Date.now(),
    orderNumber: orderNumber,
    created_at: new Date().toISOString(),
    items: cart.map(i=>({product_id:i.id, product_name:i.name, qty:i.qty, price:i.price})),
    total: cart.reduce((s,i)=>s + i.price * i.qty, 0),
    customer_name: customerName || (user?user.control:'Anónimo'),
    payment_method: paymentMethod,
    card_last_four: cardLast4 || null,
    status: 'pendiente'
  };
  orders.unshift(newOrder);
  cart = [];
  persist(); renderCart(); renderOrders();
  toast('Pedido #' + orderNumber + ' creado');
  // generate QR & simulate progress
  simulateProgress(newOrder.id);
  return newOrder;
}

/* simulate status changes */
function simulateProgress(orderId){
  const idx = orders.findIndex(o=>o.id===orderId);
  if(idx<0) return;
  setTimeout(()=>{ orders[idx].status='en_preparacion'; persist(); renderOrders(); toast('Pedido #' + orders[idx].orderNumber + ' en preparación'); }, 3000);
  setTimeout(()=>{ orders[idx].status='listo'; persist(); renderOrders(); toast('Pedido #' + orders[idx].orderNumber + ' listo'); }, 11000);
}

/* render orders */
function renderOrders(){
  const list = el('ordersList');
  list.innerHTML = '';
  if(orders.length===0){ list.innerHTML = '<div style="color:#4a6a57">No tienes pedidos aún</div>'; return; }
  orders.forEach(o=>{
    const card = document.createElement('div'); card.className='order-card';
    const info = document.createElement('div'); info.style.flex='1';
    const title = document.createElement('div'); title.style.fontWeight='800'; title.textContent = 'Pedido #' + o.orderNumber;
    const dt = document.createElement('div'); dt.style.fontSize='12px'; dt.style.color='#3b5e49'; dt.textContent = new Date(o.created_at).toLocaleString();
    const ttl = document.createElement('div'); ttl.style.fontWeight='700'; ttl.style.color='var(--udec-green)'; ttl.textContent = formatMoney(o.total);
    const st = document.createElement('div'); st.className='status ' + o.status; st.textContent = o.status.replace('_',' ');
    info.appendChild(title); info.appendChild(dt); info.appendChild(ttl); info.appendChild(st);

    const right = document.createElement('div'); right.style.display='flex'; right.style.flexDirection='column'; right.style.alignItems='center'; right.style.gap='6px';
    const canvas = document.createElement('canvas'); canvas.width=96; canvas.height=96;
    QRCode.toCanvas(canvas, JSON.stringify({orderNumber:o.orderNumber, id:o.id}), {width:96}).catch(()=>{});
    right.appendChild(canvas);
    card.appendChild(info); card.appendChild(right);
    list.appendChild(card);
  });
}

/* -----------------------------
   UI: modals (login & checkout)
   ----------------------------- */
const modalBack = document.getElementById('modalBack');
const modalTitle = document.getElementById('modalTitle');
const modalBody = document.getElementById('modalBody');
const modalRightFooter = document.getElementById('modalRightFooter');
const modalClose = document.getElementById('modalClose');

document.getElementById('openLoginBtn').addEventListener('click', ()=> openLoginModal());
document.getElementById('btnOrders').addEventListener('click', ()=> {
  document.querySelector('html, body').scrollTop = document.getElementById('ordersBox').offsetTop;
});
function openLoginModal(){
  modalTitle.textContent = 'Inicia sesión';
  modalBody.innerHTML = `
    <div style="display:flex; gap:8px; flex-direction:column">
      <div style="height:140px; border-radius:10px; overflow:hidden;"><img src="/mnt/data/f29137ab-3fb7-4743-a3fb-7cfc5567dcc9.png" style="width:100%; height:100%; object-fit:cover; filter: brightness(.85)"></div>
      <label>Número de control</label>
      <input id="inputControl" placeholder="Ej. 202012345" style="padding:8px; border-radius:8px; border:1px solid #e6f3ea">
      <label>Contraseña</label>
      <input id="inputPass" type="password" placeholder="Contraseña (opcional)" style="padding:8px; border-radius:8px; border:1px solid #e6f3ea">
      <div style="display:flex; gap:8px;">
        <button class="btn btn-primary" id="doLoginBtn">Entrar</button>
        <button class="btn btn-ghost" id="doQuickLogin">Ingresar rápido</button>
        <button class="btn btn-ghost" id="doUploadSim">Subir imagen (simular escaneo)</button>
        <input id="fileForSim" type="file" accept="image/*" style="display:none">
      </div>
    </div>
  `;
  modalRightFooter.innerHTML = `<div style="font-size:12px;color:#3b5e49">Inicia con número de control o sube una imagen del código (simulación local).</div>`;
  modalBack.style.display = 'flex';
  // attach actions
  document.getElementById('doLoginBtn').onclick = ()=>{
    const control = document.getElementById('inputControl').value.trim();
    if(!control){ alert('Ingresa tu número de control'); return; }
    user = { control }; persist(); updateHeader(); closeModal(); toast('Bienvenido, ' + control);
  };
  document.getElementById('doQuickLogin').onclick = ()=>{
    const control = document.getElementById('inputControl').value.trim() || prompt('Ingresa tu número de control (rápido):');
    if(!control) return;
    user = { control }; persist(); updateHeader(); closeModal(); toast('Ingreso rápido: ' + control);
  };
  document.getElementById('doUploadSim').onclick = ()=> { document.getElementById('fileForSim').click(); };
  document.getElementById('fileForSim').onchange = (e)=>{
    const f = e.target.files[0];
    if(!f) return;
    const digits = (f.name.match(/\d+/g) || []).join('');
    let value = digits.length>=4 ? digits : prompt('No hay números detectados. Escribe número de control para simular:');
    if(value){ user = { control: value }; persist(); updateHeader(); closeModal(); toast('Código simulado: ' + value); }
  };
}
modalClose.onclick = closeModal;
function closeModal(){ modalBack.style.display = 'none'; }

/* -----------------------------
   Checkout modal
   ----------------------------- */
document.getElementById('btnCheckout').addEventListener('click', ()=>{
  if(!user){ openLoginModal(); return; }
  if(cart.length===0){ alert('Carrito vacío'); return; }
  modalTitle.textContent = 'Finalizar pedido';
  modalBody.innerHTML = `
    <div style="display:flex; gap:10px;">
      <div style="flex:1">
        <div style="font-weight:700; margin-bottom:8px">Resumen</div>
        <div id="modalItems"></div>
        <div style="margin-top:8px; font-weight:800">Total: <span id="modalTotal"></span></div>
      </div>
      <div style="width:260px;">
        <div style="font-weight:700; margin-bottom:8px">Pago</div>
        <div style="display:flex; gap:6px;">
          <label><input type="radio" name="pm" value="card" checked> Tarjeta</label>
          <label><input type="radio" name="pm" value="cash"> Efectivo</label>
        </div>
        <div id="cardFields" style="margin-top:8px">
          <input id="payName" placeholder="Nombre completo" style="width:100%; padding:8px; margin-top:6px; border-radius:8px; border:1px solid #eef6ee">
          <input id="payCard" placeholder="1234 5678 9012 3456" maxlength="19" style="width:100%; padding:8px; margin-top:6px; border-radius:8px; border:1px solid #eef6ee">
          <div style="display:flex; gap:6px; margin-top:6px">
            <input id="payExp" placeholder="MM/AA" maxlength="5" style="flex:1; padding:8px; border-radius:8px; border:1px solid #eef6ee">
            <input id="payCvv" placeholder="CVV" maxlength="3" style="width:80px; padding:8px; border-radius:8px; border:1px solid #eef6ee">
          </div>
        </div>
        <div id="cashNotice" style="display:none; margin-top:8px; font-size:13px; color:#36583f">Pagarás en efectivo al recoger/recibir tu pedido.</div>
        <div style="margin-top:12px;">
          <button class="btn btn-primary" id="confirmPayBtn">Confirmar y pagar</button>
        </div>
      </div>
    </div>
  `;
  // fill items
  const mi = document.getElementById('modalItems'); mi.innerHTML = '';
  cart.forEach(it=> { mi.innerHTML += `<div style="display:flex; justify-content:space-between; margin-bottom:6px"><div>${it.name} x${it.qty}</div><div>${formatMoney(it.price * it.qty)}</div></div>`; });
  el('modalTotal').textContent = formatMoney(cart.reduce((s,i)=>s + i.price * i.qty, 0));

  // toggle payment fields
  document.querySelectorAll('input[name="pm"]').forEach(r=>{
    r.onchange = (e)=> {
      const v = e.target.value;
      if(v==='card'){ document.getElementById('cardFields').style.display='block'; document.getElementById('cashNotice').style.display='none'; }
      else { document.getElementById('cardFields').style.display='none'; document.getElementById('cashNotice').style.display='block'; }
    };
  });

  // confirm pay
  document.getElementById('confirmPayBtn').onclick = ()=>{
    const method = document.querySelector('input[name="pm"]:checked').value;
    const name = document.getElementById('payName').value.trim() || user.control;
    if(method==='card'){
      const card = document.getElementById('payCard').value.replace(/\s/g,'');
      const exp = document.getElementById('payExp').value;
      const cvv = document.getElementById('payCvv').value;
      if(card.length !== 16 || cvv.length !== 3 || !/^\d{2}\/\d{2}$/.test(exp)){ alert('Datos de tarjeta inválidos'); return; }
      const last4 = card.slice(-4);
      const ord = createOrder('Tarjeta', name, last4);
      closeModal();
      openOrderDetail(ord);
    } else {
      const ord = createOrder('Efectivo', name, null);
      closeModal();
      openOrderDetail(ord);
    }
  };

  modalBack.style.display='flex';
});

/* -----------------------------
   Open order detail (after creating)
   ----------------------------- */
function openOrderDetail(order){
  if(!order) return;
  modalTitle.textContent = 'Detalle Pedido #' + order.orderNumber;
  modalBody.innerHTML = `
    <div style="display:flex; gap:10px;">
      <div style="flex:1">
        <div style="font-weight:800; margin-bottom:8px">Pedido #${order.orderNumber}</div>
        <div style="font-size:13px; color:#3b5e49">Cliente: ${order.customer_name}</div>
        <div style="margin-top:8px" id="orderItemsList"></div>
        <div style="margin-top:10px; font-weight:700">Total: ${formatMoney(order.total)}</div>
      </div>
      <div style="width:200px; display:flex; flex-direction:column; align-items:center;">
        <div id="qrCanvas"></div>
        <div style="margin-top:10px; font-size:12px; color:#36583f">Escanea para ver tu pedido</div>
      </div>
    </div>
  `;
  const itemBox = document.getElementById('orderItemsList');
  order.items.forEach(it => { itemBox.innerHTML += `<div style="display:flex; justify-content:space-between; margin-bottom:6px"><div>${it.product_name} x${it.qty}</div><div>${formatMoney(it.price * it.qty)}</div></div>`; });
  // draw QR
  const qdiv = document.getElementById('qrCanvas'); qdiv.innerHTML=''; const canvas = document.createElement('canvas'); canvas.width=160; canvas.height=160; qdiv.appendChild(canvas);
  QRCode.toCanvas(canvas, JSON.stringify({ orderNumber: order.orderNumber, id: order.id }), { width:160 }).catch(()=>{});
  modalBack.style.display='flex';
}

/* -----------------------------
   Header / quick UI
   ----------------------------- */
function updateHeader(){
  const tag = el('welcomeTag');
  if(user){ tag.textContent = 'Perfil: ' + user.control; el('openLoginBtn').textContent = 'Perfil'; }
  else { tag.textContent = 'Bienvenido — Inicia sesión'; el('openLoginBtn').textContent = 'Iniciar sesión'; }
}

/* -----------------------------
   Toast
   ----------------------------- */
let toastTimeout = null;
function toast(msg){
  const t = document.createElement('div'); t.textContent = msg;
  t.style.position='fixed'; t.style.right='18px'; t.style.bottom='18px'; t.style.background='var(--udec-green)'; t.style.color='white';
  t.style.padding='10px 14px'; t.style.borderRadius='999px'; t.style.boxShadow='0 10px 30px rgba(15,138,63,0.12)';
  document.body.appendChild(t);
  clearTimeout(toastTimeout);
  toastTimeout = setTimeout(()=>{ t.style.transition='opacity .3s'; t.style.opacity='0'; setTimeout(()=>t.remove(),350); }, 1800);
}

/* -----------------------------
   Small interactions
   ----------------------------- */
el('clearSearch').addEventListener('click', ()=>{ el('search').value=''; buildGrid(''); });
el('search').addEventListener('input', (e)=>{ buildGrid(e.target.value); });

document.querySelectorAll('.cat').forEach(c=>{
  c.onclick = ()=> {
    const cat = c.dataset.cat;
    if(cat==='bebidas'){ buildGrid(''); scrollToFirst('FRÍAS'); } 
    else if(cat==='comidas'){ buildGrid(''); scrollToFirst('ENTRADA'); }
    else if(cat==='snacks'){ buildGrid(''); scrollToFirst('SALUDABLE'); }
    else buildGrid('');
  };
});
function scrollToFirst(text){
  // find first card whose thumb has this text, scroll into view
  const nodes = document.querySelectorAll('.thumb');
  for(const n of nodes){ if(n.textContent.includes(text)){ n.scrollIntoView({behavior:'smooth', block:'center'}); break; } }
}

el('tabAll').addEventListener('click', ()=> buildGrid(''));
el('tabFrio').addEventListener('click', ()=> buildGrid('frappé, iced, limonada, iced, frappé, frappé'));
el('tabCaliente').addEventListener('click', ()=> buildGrid('capuchino, americano, latte, chocolate'));
el('tabEntradas').addEventListener('click', ()=> buildGrid('ensalada, sopa, nachos, rollitos'));

el('btnClear').addEventListener('click', ()=>{ if(confirm('Vaciar carrito?')) { cart=[]; persist(); renderCart(); } });

el('btnOrders').addEventListener('click', ()=> {
  const top = document.getElementById('ordersBox').offsetTop - 20;
  window.scrollTo({top, behavior:'smooth'});
});

/* init */
countAll(); buildGrid(''); renderCart(); renderOrders(); updateHeader();
persist();

/* small keyboard: L opens login */
window.addEventListener('keydown', (e)=>{ if(e.key.toLowerCase()==='l') openLoginModal(); });

</script>
</body>
</html>
