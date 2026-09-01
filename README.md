<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Ledger — Simple Books</title>
<style>
  :root{
    --paper: #EFEAE0;
    --panel: #FBF9F3;
    --ink: #1F2E23;
    --ink-soft: #4A5A4E;
    --rule: #C9C0AC;
    --rule-soft: #DED7C4;
    --brass: #9C6B25;
    --brass-soft: #E8DBC2;
    --red: #8C3B2E;
    --green: #2E5B3E;
    --font-display: Georgia, 'Iowan Old Style', 'Palatino Linotype', serif;
    --font-body: -apple-system, BlinkMacSystemFont, 'Segoe UI', Helvetica, Arial, sans-serif;
    --font-mono: ui-monospace, 'SFMono-Regular', Menlo, Consolas, monospace;
  }
  *{ box-sizing: border-box; }
  html,body{ height:100%; }
  body{
    margin:0;
    background:var(--paper);
    color:var(--ink);
    font-family:var(--font-body);
    display:flex;
    min-height:100vh;
  }
  /* Sidebar */
  #sidebar{
    width:210px;
    flex-shrink:0;
    background:var(--panel);
    border-right:1px solid var(--rule);
    padding:22px 16px;
    display:flex;
    flex-direction:column;
    gap:22px;
  }
  #brand{
    font-family:var(--font-display);
    font-size:22px;
    letter-spacing:0.5px;
    color:var(--ink);
    border-bottom: 2px solid var(--ink);
    padding-bottom:10px;
  }
  #brand span{
    display:block;
    font-family:var(--font-body);
    font-size:11px;
    color:var(--ink-soft);
    font-weight:400;
    margin-top:2px;
  }
  .navgroup-label{
    font-size:11px;
    color:var(--ink-soft);
    margin:0 0 6px 2px;
    font-weight:600;
  }
  .navgroup{ display:flex; flex-direction:column; gap:1px; }
  .navitem{
    padding:7px 8px;
    font-size:14px;
    color:var(--ink);
    cursor:pointer;
    border-left:2px solid transparent;
    text-decoration:none;
  }
  .navitem:hover{ background:var(--brass-soft); }
  .navitem.active{
    border-left:2px solid var(--brass);
    background:var(--brass-soft);
    font-weight:600;
  }
  /* Main */
  #main{
    flex:1;
    padding:28px 36px 60px;
    max-width:980px;
  }
  h1{
    font-family:var(--font-display);
    font-size:26px;
    margin:0 0 4px;
  }
  .subtitle{ color:var(--ink-soft); font-size:13px; margin:0 0 22px; }
  h2{
    font-family:var(--font-display);
    font-size:16px;
    margin:28px 0 10px;
    border-bottom:1px solid var(--rule);
    padding-bottom:6px;
  }
  table{ width:100%; border-collapse:collapse; font-size:14px; }
  th{
    text-align:left;
    font-size:11px;
    text-transform:none;
    color:var(--ink-soft);
    font-weight:600;
    border-bottom:1px solid var(--ink);
    padding:6px 8px;
  }
  td{
    padding:7px 8px;
    border-bottom:1px solid var(--rule-soft);
  }
  td.num, th.num{ text-align:right; font-family:var(--font-mono); }
  tr.total-row td{ border-top:2px solid var(--ink); border-bottom:none; font-weight:600; font-family:var(--font-mono); }
  .empty{
    padding:22px 8px;
    color:var(--ink-soft);
    font-size:14px;
    border:1px dashed var(--rule);
    background:var(--panel);
  }
  /* Form */
  form.voucher-form{
    background:var(--panel);
    border:1px solid var(--rule);
    padding:18px 20px;
    max-width:460px;
  }
  .field{ margin-bottom:14px; display:flex; flex-direction:column; gap:4px; }
  .field label{ font-size:12px; color:var(--ink-soft); font-weight:600; }
  .field input, .field select, .field textarea{
    font-family:var(--font-body);
    font-size:14px;
    padding:7px 8px;
    border:1px solid var(--rule);
    background:#fff;
    color:var(--ink);
  }
  .field input:focus, .field select:focus, .field textarea:focus{
    outline:2px solid var(--brass);
    outline-offset:-1px;
  }
  .row2{ display:flex; gap:12px; }
  .row2 .field{ flex:1; }
  button{
    font-family:var(--font-body);
    font-size:14px;
    font-weight:600;
    padding:9px 18px;
    background:var(--ink);
    color:var(--panel);
    border:none;
    cursor:pointer;
  }
  button:hover{ background:var(--green); }
  button.secondary{
    background:transparent;
    color:var(--red);
    padding:4px 8px;
    font-weight:400;
    font-size:12px;
    text-decoration:underline;
  }
  button.secondary:hover{ background:transparent; color:var(--red); opacity:0.7; }
  .error{ color:var(--red); font-size:12px; margin-top:2px; }
  .tag{
    font-size:11px;
    padding:1px 6px;
    border:1px solid var(--rule);
    color:var(--ink-soft);
  }
  .tag.dr{ color:var(--green); border-color:var(--green); }
  .tag.cr{ color:var(--red); border-color:var(--red); }
  .kpis{ display:flex; gap:14px; flex-wrap:wrap; margin-bottom:8px; }
  .kpi{
    background:var(--panel);
    border:1px solid var(--rule);
    padding:14px 18px;
    min-width:160px;
  }
  .kpi .label{ font-size:11px; color:var(--ink-soft); font-weight:600; }
  .kpi .value{ font-family:var(--font-mono); font-size:22px; margin-top:4px; }
  .bs-columns{ display:flex; gap:28px; }
  .bs-columns > div{ flex:1; }
  #toast{
    position:fixed; bottom:20px; right:20px;
    background:var(--ink); color:var(--panel);
    padding:10px 16px; font-size:13px; display:none;
  }
</style>
</head>
<body>

<div id="sidebar">
  <div id="brand">Ledger<span>simple books</span></div>

  <div>
    <div class="navgroup-label">OVERVIEW</div>
    <div class="navgroup">
      <div class="navitem" data-page="dashboard">Dashboard</div>
    </div>
  </div>

  <div>
    <div class="navgroup-label">VOUCHERS</div>
    <div class="navgroup">
      <div class="navitem" data-page="sales">Sales</div>
      <div class="navitem" data-page="purchase">Purchase</div>
      <div class="navitem" data-page="receipt">Receipt</div>
      <div class="navitem" data-page="payment">Payment</div>
    </div>
  </div>

  <div>
    <div class="navgroup-label">MASTERS</div>
    <div class="navgroup">
      <div class="navitem" data-page="ledgers">Ledgers</div>
    </div>
  </div>

  <div>
    <div class="navgroup-label">REPORTS</div>
    <div class="navgroup">
      <div class="navitem" data-page="balancesheet">Balance Sheet</div>
      <div class="navitem" data-page="pl">Profit &amp; Loss</div>
      <div class="navitem" data-page="outstanding">Outstanding</div>
    </div>
  </div>
</div>

<div id="main"></div>
<div id="toast"></div>

<script>
const SYS = { cash: 'sys-cash', sales: 'sys-sales', purchase: 'sys-purchase' };

function defaultLedgers(){
  return [
    { id: SYS.cash, name: 'Cash', type: 'cash', system: true },
    { id: SYS.sales, name: 'Sales Account', type: 'sales', system: true },
    { id: SYS.purchase, name: 'Purchase Account', type: 'purchase', system: true },
  ];
}

const state = {
  page: 'dashboard',
  ledgers: [],
  vouchers: [],
  loaded: false,
};

// ---------- Persistence ----------
async function loadData(){
  let ledgersLoaded = false, vouchersLoaded = false;
  try{
    const l = await window.storage.get('app-ledgers');
    if(l && l.value){ state.ledgers = JSON.parse(l.value); ledgersLoaded = true; }
  }catch(e){ /* key doesn't exist yet */ }
  try{
    const v = await window.storage.get('app-vouchers');
    if(v && v.value){ state.vouchers = JSON.parse(v.value); vouchersLoaded = true; }
  }catch(e){ /* key doesn't exist yet */ }

  if(!ledgersLoaded){
    state.ledgers = defaultLedgers();
    await saveLedgers();
  }
  if(!vouchersLoaded){
    state.vouchers = [];
  }
  state.loaded = true;
}

async function saveLedgers(){
  try{ await window.storage.set('app-ledgers', JSON.stringify(state.ledgers)); }
  catch(e){ toast('Could not save ledgers.'); console.error(e); }
}
async function saveVouchers(){
  try{ await window.storage.set('app-vouchers', JSON.stringify(state.vouchers)); }
  catch(e){ toast('Could not save vouchers.'); console.error(e); }
}

function toast(msg){
  const t = document.getElementById('toast');
  t.textContent = msg;
  t.style.display = 'block';
  clearTimeout(window._toastTimer);
  window._toastTimer = setTimeout(()=>{ t.style.display='none'; }, 2200);
}

// ---------- Accounting core ----------
function ledgerById(id){ return state.ledgers.find(l=>l.id===id); }

function getEntries(v){
  switch(v.type){
    case 'sales':    return [{ledgerId:v.partyId, debit:v.amount, credit:0}, {ledgerId:SYS.sales, debit:0, credit:v.amount}];
    case 'purchase': return [{ledgerId:SYS.purchase, debit:v.amount, credit:0}, {ledgerId:v.partyId, debit:0, credit:v.amount}];
    case 'receipt':  return [{ledgerId:v.bankId, debit:v.amount, credit:0}, {ledgerId:v.partyId, debit:0, credit:v.amount}];
    case 'payment':  return [{ledgerId:v.partyId, debit:v.amount, credit:0}, {ledgerId:v.bankId, debit:0, credit:v.amount}];
  }
  return [];
}

function computeBalance(ledgerId){
  let bal = 0;
  state.vouchers.forEach(v=>{
    getEntries(v).forEach(e=>{ if(e.ledgerId===ledgerId) bal += e.debit - e.credit; });
  });
  return bal;
}

function nextVoucherNo(type){
  const prefix = {sales:'SV', purchase:'PV', receipt:'RV', payment:'PY'}[type];
  const count = state.vouchers.filter(v=>v.type===type).length + 1;
  return prefix + '-' + String(count).padStart(4,'0');
}

function fmt(n){
  const abs = Math.abs(n).toLocaleString('en-IN', {minimumFractionDigits:2, maximumFractionDigits:2});
  return (n < -0.004 ? '−' : '') + '₹' + abs;
}
function todayStr(){ return new Date().toISOString().slice(0,10); }
function fmtDate(d){
  const dt = new Date(d + 'T00:00:00');
  return dt.toLocaleDateString('en-IN', {day:'2-digit', month:'short', year:'numeric'});
}

// ---------- Navigation ----------
document.querySelectorAll('.navitem').forEach(el=>{
  el.addEventListener('click', ()=>{ state.page = el.dataset.page; render(); });
});

// ---------- Render dispatch ----------
function render(){
  document.querySelectorAll('.navitem').forEach(el=>{
    el.classList.toggle('active', el.dataset.page === state.page);
  });
  const main = document.getElementById('main');
  const renderers = {
    dashboard: renderDashboard,
    sales: ()=>renderVoucherPage('sales'),
    purchase: ()=>renderVoucherPage('purchase'),
    receipt: ()=>renderVoucherPage('receipt'),
    payment: ()=>renderVoucherPage('payment'),
    ledgers: renderLedgers,
    balancesheet: renderBalanceSheet,
    pl: renderPL,
    outstanding: renderOutstanding,
  };
  main.innerHTML = renderers[state.page] ? renderers[state.page]() : '';
  attachPageHandlers();
}

// ---------- Dashboard ----------
function renderDashboard(){
  const debtors = state.ledgers.filter(l=>l.type==='debtor');
  const creditors = state.ledgers.filter(l=>l.type==='creditor');
  const banks = state.ledgers.filter(l=>l.type==='bank');

  const totalDebtors = debtors.reduce((s,l)=>s+computeBalance(l.id),0);
  const totalCreditors = creditors.reduce((s,l)=>s-computeBalance(l.id),0);
  const cashBank = computeBalance(SYS.cash) + banks.reduce((s,l)=>s+computeBalance(l.id),0);
  const netProfit = computeBalance(SYS.sales) - computeBalance(SYS.purchase);

  const recent = [...state.vouchers].sort((a,b)=> b.date.localeCompare(a.date) || b.id.localeCompare(a.id)).slice(0,8);

  return `
    <h1>Dashboard</h1>
    <p class="subtitle">Today, ${fmtDate(todayStr())}</p>

    <div class="kpis">
      <div class="kpi"><div class="label">SUNDRY DEBTORS</div><div class="value">${fmt(totalDebtors)}</div></div>
      <div class="kpi"><div class="label">SUNDRY CREDITORS</div><div class="value">${fmt(totalCreditors)}</div></div>
      <div class="kpi"><div class="label">CASH &amp; BANK</div><div class="value">${fmt(cashBank)}</div></div>
      <div class="kpi"><div class="label">NET PROFIT</div><div class="value" style="color:${netProfit>=0?'var(--green)':'var(--red)'}">${fmt(netProfit)}</div></div>
    </div>

    <h2>Recent vouchers</h2>
    ${recent.length===0 ? emptyState('No vouchers yet. Start by recording a sale, purchase, receipt or payment.') : `
    <table>
      <thead><tr><th>Date</th><th>Voucher No.</th><th>Type</th><th>Party</th><th class="num">Amount</th></tr></thead>
      <tbody>
        ${recent.map(v=>`
          <tr>
            <td>${fmtDate(v.date)}</td>
            <td>${v.voucherNo}</td>
            <td style="text-transform:capitalize">${v.type}</td>
            <td>${ledgerById(v.partyId) ? ledgerById(v.partyId).name : '—'}</td>
            <td class="num">${fmt(v.amount)}</td>
          </tr>`).join('')}
      </tbody>
    </table>`}
  `;
}

function emptyState(msg){
  return `<div class="empty">${msg}</div>`;
}

// ---------- Voucher pages ----------
const VOUCHER_CONFIG = {
  sales:    { title:'Sales', partyLabel:'Party (debtor / cash / bank)', partyTypes:['debtor','cash','bank'], amountVerb:'Sale amount' },
  purchase: { title:'Purchase', partyLabel:'Party (creditor / cash / bank)', partyTypes:['creditor','cash','bank'], amountVerb:'Purchase amount' },
  receipt:  { title:'Receipt', partyLabel:'Received from (debtor / creditor)', partyTypes:['debtor','creditor'], amountVerb:'Amount received', needsBank:true },
  payment:  { title:'Payment', partyLabel:'Paid to (creditor / debtor)', partyTypes:['creditor','debtor'], amountVerb:'Amount paid', needsBank:true },
};

function ledgerOptions(types){
  return state.ledgers.filter(l=>types.includes(l.type))
    .map(l=>`<option value="${l.id}">${l.name}${l.type==='cash'?'':''}</option>`).join('');
}

function bankCashOptions(){
  return state.ledgers.filter(l=>l.type==='cash' || l.type==='bank')
    .map(l=>`<option value="${l.id}">${l.name}</option>`).join('');
}

function renderVoucherPage(type){
  const cfg = VOUCHER_CONFIG[type];
  const list = state.vouchers.filter(v=>v.type===type).sort((a,b)=> b.date.localeCompare(a.date) || b.id.localeCompare(a.id));

  const partyOptionsHtml = ledgerOptions(cfg.partyTypes);
  const noPartiesYet = partyOptionsHtml.trim() === '';

  return `
    <h1>${cfg.title} voucher</h1>
    <p class="subtitle">Voucher no. ${nextVoucherNo(type)}</p>

    ${noPartiesYet ? emptyState(`No eligible ledgers yet. Add a ${cfg.partyTypes.filter(t=>t!=='cash'&&t!=='bank').join(' or ')} account under Masters → Ledgers first.`) : `
    <form class="voucher-form" id="voucher-form" data-type="${type}">
      <div class="row2">
        <div class="field">
          <label>Date</label>
          <input type="date" name="date" value="${todayStr()}" required>
        </div>
      </div>
      <div class="field">
        <label>${cfg.partyLabel}</label>
        <select name="partyId" required>
          <option value="" disabled selected>Select ledger</option>
          ${partyOptionsHtml}
        </select>
      </div>
      ${cfg.needsBank ? `
      <div class="field">
        <label>${type==='receipt' ? 'Deposited to' : 'Paid from'} (cash / bank)</label>
        <select name="bankId" required>
          <option value="" disabled selected>Select account</option>
          ${bankCashOptions()}
        </select>
      </div>` : ''}
      <div class="field">
        <label>${cfg.amountVerb}</label>
        <input type="number" name="amount" min="0.01" step="0.01" placeholder="0.00" required>
      </div>
      <div class="field">
        <label>Narration (optional)</label>
        <textarea name="narration" rows="2"></textarea>
      </div>
      <div id="voucher-error" class="error"></div>
      <button type="submit">Save ${cfg.title.toLowerCase()} voucher</button>
    </form>`}

    <h2>${cfg.title} register</h2>
    ${list.length===0 ? emptyState(`No ${cfg.title.toLowerCase()} vouchers recorded yet.`) : `
    <table>
      <thead><tr><th>Date</th><th>Voucher No.</th><th>Party</th><th class="num">Amount</th><th>Narration</th><th></th></tr></thead>
      <tbody>
        ${list.map(v=>`
          <tr>
            <td>${fmtDate(v.date)}</td>
            <td>${v.voucherNo}</td>
            <td>${ledgerById(v.partyId) ? ledgerById(v.partyId).name : '(deleted)'}</td>
            <td class="num">${fmt(v.amount)}</td>
            <td>${v.narration || ''}</td>
            <td><button class="secondary" data-delete-voucher="${v.id}">delete</button></td>
          </tr>`).join('')}
      </tbody>
    </table>`}
  `;
}

// ---------- Ledgers (masters) ----------
function renderLedgers(){
  const debtors = state.ledgers.filter(l=>l.type==='debtor');
  const creditors = state.ledgers.filter(l=>l.type==='creditor');
  const banks = state.ledgers.filter(l=>l.type==='bank');

  function group(title, items, drIsPositive){
    if(items.length===0) return `<h2>${title}</h2>${emptyState(`No ${title.toLowerCase()} added yet.`)}`;
    return `
      <h2>${title}</h2>
      <table>
        <thead><tr><th>Name</th><th class="num">Balance</th><th></th></tr></thead>
        <tbody>
          ${items.map(l=>{
            const bal = computeBalance(l.id);
            const dr = drIsPositive ? bal >= 0 : bal < 0;
            return `<tr>
              <td>${l.name}</td>
              <td class="num">${fmt(Math.abs(bal))} <span class="tag ${dr?'dr':'cr'}">${dr?'Dr':'Cr'}</span></td>
              <td>${l.system ? '' : `<button class="secondary" data-delete-ledger="${l.id}">delete</button>`}</td>
            </tr>`;
          }).join('')}
        </tbody>
      </table>`;
  }

  return `
    <h1>Ledgers</h1>
    <p class="subtitle">Masters — creditors, debtors and bank accounts</p>

    <form class="voucher-form" id="ledger-form">
      <div class="field">
        <label>Ledger name</label>
        <input type="text" name="name" placeholder="e.g. Sharma Traders" required>
      </div>
      <div class="field">
        <label>Type</label>
        <select name="type" required>
          <option value="debtor">Debtor (customer / sundry debtor)</option>
          <option value="creditor">Creditor (supplier / sundry creditor)</option>
          <option value="bank">Bank account</option>
        </select>
      </div>
      <div id="ledger-error" class="error"></div>
      <button type="submit">Add ledger</button>
    </form>

    ${group('Debtors', debtors, true)}
    ${group('Creditors', creditors, false)}
    ${group('Bank accounts', banks, true)}
    <h2>Cash</h2>
    <table>
      <thead><tr><th>Name</th><th class="num">Balance</th></tr></thead>
      <tbody><tr><td>Cash</td><td class="num">${fmt(Math.abs(computeBalance(SYS.cash)))} <span class="tag ${computeBalance(SYS.cash)>=0?'dr':'cr'}">${computeBalance(SYS.cash)>=0?'Dr':'Cr'}</span></td></tr></tbody>
    </table>
  `;
}

// ---------- Reports ----------
function renderBalanceSheet(){
  const debtors = state.ledgers.filter(l=>l.type==='debtor');
  const creditors = state.ledgers.filter(l=>l.type==='creditor');
  const banks = state.ledgers.filter(l=>l.type==='bank');

  const totalDebtors = debtors.reduce((s,l)=>s+computeBalance(l.id),0);
  const totalCreditors = creditors.reduce((s,l)=>s - computeBalance(l.id),0);
  const cash = computeBalance(SYS.cash);
  const totalBank = banks.reduce((s,l)=>s+computeBalance(l.id),0);
  const totalAssets = totalDebtors + cash + totalBank;
  const capitalAndReserves = totalAssets - totalCreditors;

  const totalLiabilities = totalCreditors + capitalAndReserves;

  return `
    <h1>Balance sheet</h1>
    <p class="subtitle">As on ${fmtDate(todayStr())}</p>
    <div class="bs-columns">
      <div>
        <h2>Liabilities</h2>
        <table>
          <tbody>
            <tr><td>Sundry Creditors</td><td class="num">${fmt(totalCreditors)}</td></tr>
            <tr><td>Capital &amp; Reserves <span class="tag">incl. current profit</span></td><td class="num">${fmt(capitalAndReserves)}</td></tr>
            <tr class="total-row"><td>Total</td><td class="num">${fmt(totalLiabilities)}</td></tr>
          </tbody>
        </table>
      </div>
      <div>
        <h2>Assets</h2>
        <table>
          <tbody>
            <tr><td>Sundry Debtors</td><td class="num">${fmt(totalDebtors)}</td></tr>
            <tr><td>Cash</td><td class="num">${fmt(cash)}</td></tr>
            <tr><td>Bank accounts</td><td class="num">${fmt(totalBank)}</td></tr>
            <tr class="total-row"><td>Total</td><td class="num">${fmt(totalAssets)}</td></tr>
          </tbody>
        </table>
      </div>
    </div>
    <p class="subtitle" style="margin-top:16px;">Capital &amp; Reserves is a balancing figure (assets minus creditors) since no separate capital ledger is maintained — it already includes the current profit or loss.</p>
  `;
}

function renderPL(){
  const sales = computeBalance(SYS.sales) * -1; // sales is credit-natured
  const purchases = computeBalance(SYS.purchase);
  const profit = sales - purchases;

  return `
    <h1>Profit &amp; loss</h1>
    <p class="subtitle">For the period ending ${fmtDate(todayStr())}</p>
    <div class="bs-columns">
      <div>
        <h2>Debit</h2>
        <table>
          <tbody>
            <tr><td>Purchase Account</td><td class="num">${fmt(purchases)}</td></tr>
            ${profit >= 0 ? `<tr class="total-row"><td>Net Profit</td><td class="num">${fmt(profit)}</td></tr>` : ''}
            <tr class="total-row"><td>Total</td><td class="num">${fmt(Math.max(sales,purchases))}</td></tr>
          </tbody>
        </table>
      </div>
      <div>
        <h2>Credit</h2>
        <table>
          <tbody>
            <tr><td>Sales Account</td><td class="num">${fmt(sales)}</td></tr>
            ${profit < 0 ? `<tr class="total-row"><td>Net Loss</td><td class="num">${fmt(-profit)}</td></tr>` : ''}
            <tr class="total-row"><td>Total</td><td class="num">${fmt(Math.max(sales,purchases))}</td></tr>
          </tbody>
        </table>
      </div>
    </div>
    <div class="kpis" style="margin-top:20px;">
      <div class="kpi">
        <div class="label">${profit>=0 ? 'NET PROFIT' : 'NET LOSS'}</div>
        <div class="value" style="color:${profit>=0?'var(--green)':'var(--red)'}">${fmt(Math.abs(profit))}</div>
      </div>
    </div>
  `;
}

function renderOutstanding(){
  const debtors = state.ledgers.filter(l=>l.type==='debtor').map(l=>({...l, bal: computeBalance(l.id)}));
  const creditors = state.ledgers.filter(l=>l.type==='creditor').map(l=>({...l, bal: computeBalance(l.id)}));
  const totalDebtors = debtors.reduce((s,l)=>s+l.bal,0);
  const totalCreditors = creditors.reduce((s,l)=>s-l.bal,0);

  function table(items, drIsPositive){
    if(items.length===0) return emptyState('Nothing outstanding here.');
    return `
      <table>
        <thead><tr><th>Ledger</th><th class="num">Outstanding</th></tr></thead>
        <tbody>
          ${items.map(l=>{
            const dr = drIsPositive ? l.bal >= 0 : l.bal < 0;
            return `<tr><td>${l.name}</td><td class="num">${fmt(Math.abs(l.bal))} <span class="tag ${dr?'dr':'cr'}">${dr?'Dr':'Cr'}</span></td></tr>`;
          }).join('')}
        </tbody>
      </table>`;
  }

  return `
    <h1>Outstanding</h1>
    <p class="subtitle">Receivables and payables as on ${fmtDate(todayStr())}</p>

    <h2>Debtors — receivable (${fmt(totalDebtors)})</h2>
    ${table(debtors, true)}

    <h2>Creditors — payable (${fmt(totalCreditors)})</h2>
    ${table(creditors, false)}
  `;
}

// ---------- Event handlers for dynamic content ----------
function attachPageHandlers(){
  const vForm = document.getElementById('voucher-form');
  if(vForm){
    vForm.addEventListener('submit', async (e)=>{
      e.preventDefault();
      const type = vForm.dataset.type;
      const fd = new FormData(vForm);
      const amount = parseFloat(fd.get('amount'));
      const partyId = fd.get('partyId');
      const bankId = fd.get('bankId');
      const errEl = document.getElementById('voucher-error');

      if(!partyId){ errEl.textContent = 'Please select a party.'; return; }
      if(VOUCHER_CONFIG[type].needsBank && !bankId){ errEl.textContent = 'Please select a cash/bank account.'; return; }
      if(!(amount > 0)){ errEl.textContent = 'Amount must be greater than zero.'; return; }
      if(VOUCHER_CONFIG[type].needsBank && bankId === partyId){ errEl.textContent = 'Party and cash/bank account cannot be the same.'; return; }

      const voucher = {
        id: crypto.randomUUID(),
        type,
        voucherNo: nextVoucherNo(type),
        date: fd.get('date') || todayStr(),
        partyId,
        bankId: bankId || undefined,
        amount,
        narration: fd.get('narration') || '',
      };
      state.vouchers.push(voucher);
      await saveVouchers();
      toast(`${VOUCHER_CONFIG[type].title} voucher ${voucher.voucherNo} saved.`);
      render();
    });
  }

  document.querySelectorAll('[data-delete-voucher]').forEach(btn=>{
    btn.addEventListener('click', async ()=>{
      if(!confirm('Delete this voucher? This cannot be undone.')) return;
      const id = btn.dataset.deleteVoucher;
      state.vouchers = state.vouchers.filter(v=>v.id!==id);
      await saveVouchers();
      toast('Voucher deleted.');
      render();
    });
  });

  const lForm = document.getElementById('ledger-form');
  if(lForm){
    lForm.addEventListener('submit', async (e)=>{
      e.preventDefault();
      const fd = new FormData(lForm);
      const name = (fd.get('name') || '').trim();
      const type = fd.get('type');
      const errEl = document.getElementById('ledger-error');
      if(!name){ errEl.textContent = 'Please enter a ledger name.'; return; }
      if(state.ledgers.some(l=>l.name.toLowerCase() === name.toLowerCase())){
        errEl.textContent = 'A ledger with this name already exists.'; return;
      }
      state.ledgers.push({ id: crypto.randomUUID(), name, type });
      await saveLedgers();
      toast(`${name} added.`);
      lForm.reset();
      render();
    });
  }

  document.querySelectorAll('[data-delete-ledger]').forEach(btn=>{
    btn.addEventListener('click', async ()=>{
      const id = btn.dataset.deleteLedger;
      const inUse = state.vouchers.some(v=>v.partyId===id || v.bankId===id);
      if(inUse){ toast('Cannot delete — this ledger has vouchers recorded against it.'); return; }
      if(!confirm('Delete this ledger?')) return;
      state.ledgers = state.ledgers.filter(l=>l.id!==id);
      await saveLedgers();
      toast('Ledger deleted.');
      render();
    });
  });
}

// ---------- Init ----------
(async function init(){
  document.getElementById('main').innerHTML = `<p class="subtitle">Loading your books…</p>`;
  await loadData();
  render();
})();
</script>
</body>
</html>
