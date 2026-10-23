<!DOCTYPE html>
<html lang="th">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>NW SHOP</title>
<style>
:root{--g:#4caf50;--gd:#2e7d32;--y:#ffc107;--bg:#fffbe9;--card:#fff;--t:#3b2f1e;--m:#8a7a60;--r:#e53935}
*{box-sizing:border-box}
body{margin:0;font-family:"Sarabun","Noto Sans Thai",system-ui,sans-serif;background:var(--bg);color:var(--t)}
header{background:linear-gradient(135deg,var(--g),var(--gd));color:#fff;padding:14px 16px;position:sticky;top:0;z-index:10;display:flex;align-items:center;gap:10px}
header h1{font-size:20px;margin:0;flex:1}
button{font:inherit;cursor:pointer;border:0;border-radius:10px;padding:9px 14px;background:var(--y);color:#3b2f1e;font-weight:700}
button.sec{background:#fff;color:var(--gd);border:1px solid var(--g)}
button.del{background:var(--r);color:#fff}
.wrap{max-width:1000px;margin:0 auto;padding:14px}
.hero{background:#fff3c4;border-radius:16px;padding:16px;margin-bottom:12px}
.hero h2{margin:0 0 4px}
input,select,textarea{font:inherit;width:100%;padding:9px;border:1px solid #d9cfae;border-radius:10px;background:#fff}
.bar{display:flex;gap:8px;flex-wrap:wrap;margin-bottom:12px}
.bar input{flex:1;min-width:160px}
.cats{display:flex;gap:6px;overflow-x:auto;padding-bottom:6px;margin-bottom:10px}
.cats button{white-space:nowrap;background:#fff;border:1px solid #e0d5a8}
.cats button.on{background:var(--g);color:#fff}
.grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(155px,1fr));gap:10px}
.card{background:var(--card);border-radius:14px;padding:12px;text-align:center;box-shadow:0 2px 6px #0001;display:flex;flex-direction:column;gap:4px}
.emo{font-size:44px}
.card .n{font-weight:700}.card .d{font-size:12px;color:var(--m);flex:1}
.price{color:var(--gd);font-weight:800;font-size:18px}
.out{opacity:.5}
.modal{position:fixed;inset:0;background:#0008;display:none;align-items:flex-end;justify-content:center;z-index:50}
.modal.show{display:flex}
.sheet{background:#fff;width:100%;max-width:640px;max-height:92vh;overflow:auto;border-radius:18px 18px 0 0;padding:16px}
.row{display:flex;gap:8px;align-items:center;margin:6px 0}
.row .grow{flex:1}
.qty button{padding:2px 10px}
label{font-size:13px;color:var(--m);display:block;margin-top:8px}
table{width:100%;border-collapse:collapse;font-size:13px}
th,td{border-bottom:1px solid #eee;padding:6px;text-align:left;vertical-align:top}
.tabs{display:flex;gap:6px;margin-bottom:10px}
.tabs button{background:#fff;border:1px solid #e0d5a8}.tabs button.on{background:var(--g);color:#fff}
.badge{display:inline-block;background:var(--r);color:#fff;border-radius:99px;padding:0 7px;font-size:12px}
.ok{background:#e8f5e9;border-radius:12px;padding:12px;margin:8px 0;font-size:15px}
footer{text-align:center;color:var(--m);font-size:12px;padding:20px}
</style>
</head>
<body>
<header>
  <h1 id="shopName">🌾NW Shop</h1>
  <button class="sec" onclick="openAdmin()">แอดมิน</button>
  <button onclick="openCart()">🛒 <span class="badge" id="cartCount">0</span></button>
</header>

<div class="wrap">
  <div class="hero"><h2 id="heroTitle"></h2><div id="heroSub"></div></div>
  <div class="bar">
    <input id="q" placeholder="🔍 ค้นหาสินค้า..." oninput="render()">
    <button class="sec" onclick="openTrack()">ตรวจเลขออเดอร์</button>
  </div>
  <div class="cats" id="cats"></div>
  <div class="grid" id="grid"></div>
</div>
<footer>ร้านนี้ไม่เกี่ยวข้องกับ Supercell · ติดต่อสั่งซื้อผ่าน LINE ได้เลย</footer>

<!-- ตะกร้า -->
<div class="modal" id="mCart" onclick="if(event.target===this)closeM('mCart')"><div class="sheet" id="cartBody"></div></div>
<!-- ตรวจออเดอร์ -->
<div class="modal" id="mTrack" onclick="if(event.target===this)closeM('mTrack')"><div class="sheet">
  <h3>ตรวจสอบออเดอร์</h3>
  <input id="trackNo" placeholder="เช่น HD-261023-001">
  <div class="row"><button onclick="track()">ค้นหา</button><button class="sec" onclick="closeM('mTrack')">ปิด</button></div>
  <div id="trackRes"></div>
</div></div>
<!-- แอดมิน -->
<div class="modal" id="mAdmin" onclick="if(event.target===this)closeM('mAdmin')"><div class="sheet" id="adminBody"></div></div>

<script>
/* ============================================================
   ✏️ แก้ไขตรงนี้ได้เลย (ชื่อร้าน / LINE / รหัสแอดมิน / สินค้าเริ่มต้น)
   ============================================================ */
const CONFIG = {
  shopName: "🌾 NW SHOP",
  heroTitle: "ขายของHay day หาให้ได้ทุกอย่างที่หาได้",
  heroSub: "เลือกสินค้า → กดสั่งซื้อ → รับเลขออเดอร์ → ส่งให้แอดมินทาง Facebook",
  Facebook: "",  Wisitsak Tupthaworn        // Facebook ของร้าน
  adminPin: "230452",           // รหัสเข้าหน้าแอดมิน (เปลี่ยนด้วย!)
  currency: "฿",
  orderPrefix: "HD",
  statuses: ["รอชำระเงิน","ชำระแล้ว","กำลังจัดส่ง","สำเร็จ","ยกเลิก"]
};
const DEFAULT_PRODUCTS = [
  {id:1,cat:"เหรียญ",emoji:"🪙",name:"เหรียญ 100,000",desc:"เหรียญในเกม",price:79,stock:99},
  {id:2,cat:"เหรียญ",emoji:"🪙",name:"เหรียญ 500,000",desc:"เหรียญในเกม",price:349,stock:99},
  {id:3,cat:"เพชร",emoji:"💎",name:"เพชร 100",desc:"เพชรในเกม",price:59,stock:99},
  {id:4,cat:"เพชร",emoji:"💎",name:"เพชร 500",desc:"เพชรในเกม",price:279,stock:99},
  {id:5,cat:"พืชผล",emoji:"🌾",name:"ข้าวสาลี x100",desc:"วัตถุดิบพื้นฐาน",price:25,stock:99},
  {id:6,cat:"พืชผล",emoji:"🌽",name:"ข้าวโพด x100",desc:"วัตถุดิบพื้นฐาน",price:25,stock:99},
  {id:7,cat:"พืชผล",emoji:"🥕",name:"แครอท x100",desc:"วัตถุดิบพื้นฐาน",price:30,stock:99},
  {id:8,cat:"พืชผล",emoji:"🎃",name:"ฟักทอง x100",desc:"วัตถุดิบพื้นฐาน",price:35,stock:99},
  {id:9,cat:"สินค้าผลิต",emoji:"🥖",name:"ขนมปัง x50",desc:"จากโรงทำขนมปัง",price:30,stock:99},
  {id:10,cat:"สินค้าผลิต",emoji:"🧀",name:"ชีส x50",desc:"จากโรงทำชีส",price:45,stock:99},
  {id:11,cat:"สินค้าผลิต",emoji:"🍰",name:"เค้ก x50",desc:"จากโรงทำเค้ก",price:55,stock:99},
  {id:12,cat:"สินค้าผลิต",emoji:"🧣",name:"ผ้าพันคอ x30",desc:"จากโรงทอผ้า",price:60,stock:99},
  {id:13,cat:"วัสดุขยายที่ดิน",emoji:"🔨",name:"ค้อน x20",desc:"ขยายที่ดิน/ซ่อมสิ่งก่อสร้าง",price:49,stock:99},
  {id:14,cat:"วัสดุขยายที่ดิน",emoji:"⛏️",name:"พลั่ว/ขวาน/เลื่อย ชุดละ 20",desc:"เครื่องมือทำเหมือง/ขยายที่ดิน",price:79,stock:99},
  {id:15,cat:"วัสดุขยายที่ดิน",emoji:"💣",name:"ระเบิด/ไดนาไมต์ x20",desc:"วัสดุเหมือง",price:89,stock:99},
  {id:16,cat:"อัปเกรด",emoji:"🔩",name:"สลักเกลียว/ตะปู/แผ่นไม้ ชุด x30",desc:"อัปเกรดยุ้งฉาง/ไซโล",price:69,stock:99},
  {id:17,cat:"อัปเกรด",emoji:"🪵",name:"แผ่นไม้ x50",desc:"อัปเกรดยุ้งฉาง/ไซโล",price:55,stock:99},
  {id:18,cat:"Derby/กิจกรรม",emoji:"🏁",name:"ช่วยทำ Derby 1 งาน",desc:"แจ้งชื่อฟาร์ม/ไอดี",price:20,stock:99},
  {id:19,cat:"Derby/กิจกรรม",emoji:"🎟️",name:"ตั๋ว/บูสเตอร์ กิจกรรม",desc:"ตามกิจกรรมที่เปิด",price:39,stock:99},
  {id:20,cat:"ตกแต่ง",emoji:"🏡",name:"ของตกแต่งฟาร์ม (สั่งทำ)",desc:"ระบุชื่อของตกแต่งในหมายเหตุ",price:99,stock:99}
];
/* ============================================================ */

const LS = {get:(k,d)=>{try{return JSON.parse(localStorage.getItem(k))??d}catch(e){return d}},
            set:(k,v)=>{try{localStorage.setItem(k,JSON.stringify(v))}catch(e){}}};
let products = LS.get("hd_products", DEFAULT_PRODUCTS);
let orders   = LS.get("hd_orders", []);
let cart     = LS.get("hd_cart", {});
let cat = "ทั้งหมด", isAdmin = false, tab = "orders", editId = null;
const $ = id => document.getElementById(id);
const esc = s => String(s??"").replace(/[&<>"']/g,c=>({"&":"&amp;","<":"&lt;",">":"&gt;",'"':"&quot;","'":"&#39;"}[c]));
const money = n => CONFIG.currency + Number(n).toLocaleString();
const save = () => {LS.set("hd_products",products);LS.set("hd_orders",orders);LS.set("hd_cart",cart)};
const openM = id => $(id).classList.add("show");
const closeM = id => $(id).classList.remove("show");

$("shopName").textContent = CONFIG.shopName;
$("heroTitle").textContent = CONFIG.heroTitle;
$("heroSub").textContent = CONFIG.heroSub;

function render(){
  const cats = ["ทั้งหมด",...new Set(products.map(p=>p.cat))];
  $("cats").innerHTML = cats.map(c=>`<button class="${c===cat?"on":""}" onclick="cat='${esc(c)}';render()">${esc(c)}</button>`).join("");
  const q = $("q").value.trim().toLowerCase();
  const list = products.filter(p=>(cat==="ทั้งหมด"||p.cat===cat)&&(p.name+p.desc+p.cat).toLowerCase().includes(q));
  $("grid").innerHTML = list.map(p=>`
    <div class="card ${p.stock<=0?"out":""}">
      <div class="emo">${esc(p.emoji)}</div>
      <div class="n">${esc(p.name)}</div>
      <div class="d">${esc(p.desc)}</div>
      <div class="price">${money(p.price)}</div>
      <button ${p.stock<=0?"disabled":""} onclick="add(${p.id})">${p.stock<=0?"หมด":"+ ใส่ตะกร้า"}</button>
    </div>`).join("") || "<p>ไม่พบสินค้า</p>";
  $("cartCount").textContent = Object.values(cart).reduce((a,b)=>a+b,0);
}
function add(id){cart[id]=(cart[id]||0)+1;save();render()}
function setQty(id,d){cart[id]=(cart[id]||0)+d;if(cart[id]<=0)delete cart[id];save();render();openCart()}
const total = () => Object.entries(cart).reduce((s,[id,q])=>{const p=products.find(x=>x.id==id);return s+(p?p.price*q:0)},0);

function openCart(){
  const items = Object.entries(cart).map(([id,q])=>({p:products.find(x=>x.id==id),q})).filter(i=>i.p);
  if(!items.length){$("cartBody").innerHTML=`<h3>🛒 ตะกร้า</h3><p>ยังไม่มีสินค้า</p><button class="sec" onclick="closeM('mCart')">ปิด</button>`;openM("mCart");return}
  $("cartBody").innerHTML = `<h3>🛒 ตะกร้า</h3>`+items.map(({p,q})=>`
    <div class="row"><span style="font-size:26px">${esc(p.emoji)}</span>
    <div class="grow"><b>${esc(p.name)}</b><br>${money(p.price)}</div>
    <div class="qty row"><button onclick="setQty(${p.id},-1)">−</button><b>${q}</b><button onclick="setQty(${p.id},1)">+</button></div></div>`).join("")+`
    <h3>รวม ${money(total())}</h3>
    <label>ชื่อผู้สั่ง *</label><input id="cName">
    <label>ชื่อฟาร์ม / ไอดี Hay Day *</label><input id="cFarm">
    <label>LINE / เบอร์ติดต่อ *</label><input id="cContact">
    <label>หมายเหตุ</label><textarea id="cNote" rows="2"></textarea>
    <div class="row"><button onclick="checkout()">ยืนยันสั่งซื้อ</button><button class="sec" onclick="closeM('mCart')">ปิด</button></div>`;
  openM("mCart");
}
function nextOrderNo(){
  const d=new Date(),ymd=String(d.getFullYear()).slice(2)+String(d.getMonth()+1).padStart(2,"0")+String(d.getDate()).padStart(2,"0");
  const pre=`${CONFIG.orderPrefix}-${ymd}-`;
  const n=orders.filter(o=>o.no.startsWith(pre)).length+1;
  return pre+String(n).padStart(3,"0");
}
function checkout(){
  const name=$("cName").value.trim(),farm=$("cFarm").value.trim(),contact=$("cContact").value.trim();
  if(!name||!farm||!contact){alert("กรุณากรอกข้อมูลที่มี * ให้ครบ");return}
  const items=Object.entries(cart).map(([id,q])=>{const p=products.find(x=>x.id==id);return p&&{name:p.name,price:p.price,qty:q}}).filter(Boolean);
  const o={no:nextOrderNo(),time:new Date().toLocaleString("th-TH"),name,farm,contact,note:$("cNote").value.trim(),items,total:total(),status:CONFIG.statuses[0]};
  orders.unshift(o);
  items.forEach(i=>{const p=products.find(x=>x.name===i.name);if(p)p.stock=Math.max(0,p.stock-i.qty)});
  cart={};save();render();
  const text=summary(o);
  $("cartBody").innerHTML=`<h3>✅ สั่งซื้อสำเร็จ</h3>
    <div class="ok">เลขออเดอร์ของคุณ<br><b style="font-size:22px">${o.no}</b></div>
    <p>คัดลอกข้อความด้านล่างแล้วส่งให้แอดมินทาง LINE <b>${esc(CONFIG.line)}</b> เพื่อชำระเงิน</p>
    <textarea id="sumTxt" rows="9" readonly>${esc(text)}</textarea>
    <div class="row"><button onclick="navigator.clipboard.writeText($('sumTxt').value).then(()=>alert('คัดลอกแล้ว'))">คัดลอก</button><button class="sec" onclick="closeM('mCart')">ปิด</button></div>`;
}
function summary(o){
  return `📦 ออเดอร์ ${o.no}\nชื่อ: ${o.name}\nฟาร์ม/ไอดี: ${o.farm}\nติดต่อ: ${o.contact}\n`+
    o.items.map(i=>`- ${i.name} x${i.qty} = ${money(i.price*i.qty)}`).join("\n")+`\nรวม: ${money(o.total)}`+(o.note?`\nหมายเหตุ: ${o.note}`:"");
}
function openTrack(){$("trackRes").innerHTML="";openM("mTrack")}
function track(){
  const o=orders.find(x=>x.no.toLowerCase()===$("trackNo").value.trim().toLowerCase());
  $("trackRes").innerHTML=o?`<div class="ok"><b>${o.no}</b><br>สถานะ: <b>${esc(o.status)}</b><br>ยอดรวม ${money(o.total)}</div>`:"<p>ไม่พบออเดอร์ (หมายเหตุ: ข้อมูลเก็บในเบราว์เซอร์นี้)</p>";
}

/* ---------- แอดมิน ---------- */
function openAdmin(){
  if(!isAdmin){const p=prompt("ใส่รหัสแอดมิน");if(p!==CONFIG.adminPin){if(p!==null)alert("รหัสไม่ถูกต้อง");return}isAdmin=true}
  renderAdmin();openM("mAdmin");
}
function renderAdmin(){
  const tabs=`<div class="tabs"><button class="${tab==="orders"?"on":""}" onclick="tab='orders';renderAdmin()">ออเดอร์ (${orders.length})</button><button class="${tab==="products"?"on":""}" onclick="tab='products';editId=null;renderAdmin()">สินค้า</button><button class="${tab==="data"?"on":""}" onclick="tab='data';renderAdmin()">ข้อมูล</button><button class="sec" onclick="closeM('mAdmin')">ปิด</button></div>`;
  let b="";
  if(tab==="orders"){
    const sales=orders.filter(o=>o.status!=="ยกเลิก").reduce((s,o)=>s+o.total,0);
    b=`<p>ยอดรวม (ไม่นับที่ยกเลิก): <b>${money(sales)}</b></p>`+(orders.map((o,i)=>`
      <div class="ok"><b>${o.no}</b> · ${esc(o.time)}<br>${esc(o.name)} / ${esc(o.farm)} / ${esc(o.contact)}<br>
      ${o.items.map(x=>`${esc(x.name)} x${x.qty}`).join(", ")}<br>รวม <b>${money(o.total)}</b>${o.note?`<br>📝 ${esc(o.note)}`:""}
      <div class="row"><select onchange="orders[${i}].status=this.value;save()">${CONFIG.statuses.map(s=>`<option ${s===o.status?"selected":""}>${s}</option>`).join("")}</select>
      <button class="del" onclick="if(confirm('ลบออเดอร์นี้?')){orders.splice(${i},1);save();renderAdmin()}">ลบ</button></div></div>`).join("")||"<p>ยังไม่มีออเดอร์</p>");
  }else if(tab==="products"){
    const p=editId!==null?(products.find(x=>x.id===editId)||{}):null;
    b=p?`<h3>${p.id?"แก้ไข":"เพิ่ม"}สินค้า</h3>
      <label>อีโมจิ</label><input id="eEmo" value="${esc(p.emoji||"📦")}">
      <label>ชื่อ</label><input id="eName" value="${esc(p.name||"")}">
      <label>หมวด</label><input id="eCat" value="${esc(p.cat||"")}">
      <label>รายละเอียด</label><input id="eDesc" value="${esc(p.desc||"")}">
      <label>ราคา</label><input id="ePrice" type="number" value="${p.price??""}">
      <label>สต็อก</label><input id="eStock" type="number" value="${p.stock??99}">
      <div class="row"><button onclick="saveProd(${p.id||0})">บันทึก</button><button class="sec" onclick="editId=null;renderAdmin()">ยกเลิก</button></div>`
    :`<button onclick="editId=0;renderAdmin()">+ เพิ่มสินค้า</button><table>`+products.map(x=>`<tr><td>${esc(x.emoji)} ${esc(x.name)}<br><small>${esc(x.cat)} · สต็อก ${x.stock}</small></td><td>${money(x.price)}</td><td><button class="sec" onclick="editId=${x.id};renderAdmin()">แก้</button> <button class="del" onclick="if(confirm('ลบสินค้า?')){products=products.filter(y=>y.id!==${x.id});save();render();renderAdmin()}">ลบ</button></td></tr>`).join("")+`</table>`;
  }else{
    b=`<p>ข้อมูลเก็บในเบราว์เซอร์เครื่องนี้ แนะนำสำรองไฟล์เป็นระยะ</p>
      <div class="row"><button onclick="exportData()">⬇️ สำรองข้อมูล (JSON)</button></div>
      <label>นำเข้าข้อมูล</label><input type="file" accept=".json" onchange="importData(this.files[0])">
      <div class="row"><button class="sec" onclick="exportCSV()">⬇️ ออเดอร์เป็น CSV</button></div>
      <div class="row"><button class="del" onclick="if(confirm('รีเซ็ตสินค้ากลับค่าเริ่มต้น?')){products=JSON.parse(JSON.stringify(DEFAULT_PRODUCTS));save();render();renderAdmin()}">รีเซ็ตสินค้า</button></div>`;
  }
  $("adminBody").innerHTML=`<h3>⚙️ หลังบ้าน</h3>`+tabs+b;
}
function saveProd(id){
  const p={id:id||Math.max(0,...products.map(x=>x.id))+1,emoji:$("eEmo").value,name:$("eName").value.trim(),cat:$("eCat").value.trim()||"อื่นๆ",desc:$("eDesc").value,price:+$("ePrice").value||0,stock:+$("eStock").value||0};
  if(!p.name){alert("ใส่ชื่อสินค้า");return}
  const i=products.findIndex(x=>x.id===p.id);i>=0?products[i]=p:products.push(p);
  editId=null;save();render();renderAdmin();
}
function dl(name,text,type){const a=document.createElement("a");a.href=URL.createObjectURL(new Blob([text],{type}));a.download=name;a.click()}
function exportData(){dl("hayday-shop-backup.json",JSON.stringify({products,orders}),"application/json")}
function importData(f){if(!f)return;f.text().then(t=>{try{const d=JSON.parse(t);products=d.products||products;orders=d.orders||orders;save();render();renderAdmin();alert("นำเข้าแล้ว")}catch(e){alert("ไฟล์ไม่ถูกต้อง")}})}
function exportCSV(){
  const rows=[["เลขออเดอร์","เวลา","ชื่อ","ฟาร์ม","ติดต่อ","รายการ","ยอดรวม","สถานะ"],...orders.map(o=>[o.no,o.time,o.name,o.farm,o.contact,o.items.map(i=>`${i.name} x${i.qty}`).join("; "),o.total,o.status])];
  dl("orders.csv","\ufeff"+rows.map(r=>r.map(c=>`"${String(c).replace(/"/g,'""')}"`).join(",")).join("\n"),"text/csv");
}
render();
</script>
</body>
</html>
