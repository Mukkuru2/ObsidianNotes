---
hp: 43
hpmax: 43
temphp: 16
hitdice: 5
hitdicemax: 6
dreadused: 0
dreadusedmax: 3
spellslotsused: 1
spellslotsusedmax: 2
magicalcunning: 0
magicalcunningmax: 1
lucky: 3
luckymax: 3
raysickness: 0
raysicknessmax: 1
holdperson: 0
holdpersonmax: 1

---



```dataviewjs
const metaedit = app.plugins.plugins["metaedit"]?.api;
if (!metaedit) { dv.paragraph("❌ MetaEdit not loaded"); return; }

const file = app.workspace.getActiveFile();
const root = this.container;

async function getVal(f) {
  const v = await metaedit.getPropertyValue(f, file);
  return v === null || v === undefined || v === "" ? 0 : Number(v);
}

async function setVal(f, v) {
  await metaedit.update(f, v, file);
  await render();
}

function makeCounter(field, label, maxField, table) {
  const row = table.insertRow();

  // Label cell
  row.insertCell().textContent = label + ":";

  // Value cell
  const valueCell = row.insertCell();
  const valueEl = valueCell.appendChild(document.createElement("span"));
  valueEl.textContent = "…";

  // Buttons cell
  const btnCell = row.insertCell();
  const minus = btnCell.appendChild(document.createElement("button"));
  minus.textContent = "➖";
  const plus = btnCell.appendChild(document.createElement("button"));
  plus.textContent = "➕";

  plus.onclick = async () => {
    let cur = await getVal(field);
    const max = maxField ? await getVal(maxField) : null;
    if (max !== null && cur >= max) return;
    await setVal(field, cur + 1);
  };

  minus.onclick = async () => {
    let cur = await getVal(field);
    if (cur <= 0) return;
    await setVal(field, cur - 1);
  };

  row.refresh = async () => {
    const cur = await getVal(field);
    const max = maxField ? await getVal(maxField) : null;
    valueEl.textContent = `${cur}${max !== null ? " / " + max : ""}`;
  };

  return row;
}

async function render() {
  root.innerHTML = "";

  const table = root.createEl("table");
  table.classList.add("stat-table");

  const counters = [
    ["hp", "Hit Points"],
    ["temphp", "Temp HP"],
    ["hitdice", "Hit Dice"],
    ["dreadused", "Dread Used"],
    ["spellslotsused", "Spell Slots Used"],
    ["magicalcunning", "Magical Cunning"],
    ["lucky", "Lucky"],
    ["raysickness", "Ray Sickness"],
    ["holdperson", "Hold Person"],
  ];

  for (const [field, label] of counters) {
    const row = makeCounter(field, label, field + "max", table);
    await row.refresh();
  }
}

render();


```
