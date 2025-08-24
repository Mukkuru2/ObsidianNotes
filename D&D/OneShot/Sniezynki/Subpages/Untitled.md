---
hp: 43
hpmax: 43
temphp: 11
hitdice: 3
hitdicemax: 6
dreadused: 3
dreadusedmax: 3
spellslotsused: 0
spellslotsusedmax: 2
magicalcunning: 0
magicalcunningmax: 1
lucky: 2
luckymax: 3
raysickness: 1
raysicknessmax: 1
holdperson: 1
holdpersonmax: 1

---



```dataviewjs
const metaedit = app.plugins.plugins["metaedit"]?.api;
if (!metaedit) { dv.paragraph("❌ MetaEdit not loaded"); return; }

const file = app.workspace.getActiveFile();
const root = this.container;

async function getVal(f) {
  const v = await metaedit.getPropertyValue(f, file);
  return v === null || v === undefined ? 0 : Number(v);
}

async function setVal(f, v) {
  await metaedit.update(f, v, file);
  // DO NOT call render or updateValues here
  rows.find(r => r.field === f).valueEl.textContent = await displayVal(f, r.maxField);
}

async function displayVal(field, maxField) {
  const cur = await getVal(field);
  const max = maxField ? await getVal(maxField) : null;
  return `${cur}${max !== null ? " / " + max : ""}`;
}

const counters = [
  { field: "hp", label: "Hit Points", max: "hpmax" },
  { field: "temphp", label: "Temp HP", max: "temphpmax" },
  { field: "hitdice", label: "Hit Dice", max: "hitdicemax" },
  { field: "dreadused", label: "Dread Used", max: "dreadusedmax" },
  { field: "spellslotsused", label: "Spell Slots Used", max: "spellslotsused_max" },
  { field: "magicalcunning", label: "Magical Cunning", max: "magicalcunning_max" },
  { field: "lucky", label: "Lucky", max: "luckymax" },
  { field: "raysickness", label: "Ray Sickness", max: "raysicknessmax" },
  { field: "holdperson", label: "Hold Person", max: "holdpersonmax" }
];

const rows = [];

function makeCounter(cfg, table) {
  const row = table.insertRow();
  row.insertCell().textContent = cfg.label + ":";
  const valueCell = row.insertCell();
  const valueEl = valueCell.appendChild(document.createElement("span"));

  const btnCell = row.insertCell();
  const minus = btnCell.appendChild(document.createElement("button"));
  minus.textContent = "➖";
  const plus = btnCell.appendChild(document.createElement("button"));
  plus.textContent = "➕";

  plus.onclick = async () => {
    let cur = await getVal(cfg.field);
    const max = cfg.max ? await getVal(cfg.max) : null;
    if (max !== null && cur >= max) return;
    await setVal(cfg.field, cur + 1);
  };

  minus.onclick = async () => {
    let cur = await getVal(cfg.field);
    if (cur <= 0) return;
    await setVal(cfg.field, cur - 1);
  };

  return { field: cfg.field, maxField: cfg.max, valueEl };
}

async function init() {
  const table = root.createEl("table");
  table.classList.add("stat-table");

  for (const cfg of counters) {
    const rowObj = makeCounter(cfg, table);
    rows.push(rowObj);
    // initialize display
    rowObj.valueEl.textContent = await displayVal(cfg.field, cfg.max);
  }
}

init();

```
