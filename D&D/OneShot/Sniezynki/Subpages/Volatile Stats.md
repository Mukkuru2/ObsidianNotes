---
hp: 43
hpmax: 43
temphp: 0
hitdice: 6
hitdicemax: 6
dreadused: 3
dreadusedmax: 3
spellslotsused: 2
spellslotsusedmax: 2
magicalcunning: 0
magicalcunningmax: 1
lucky: 3
luckymax: 3
raysickness: 1
raysicknessmax: 1
holdperson: 1
holdpersonmax: 1

---

```dataviewjs
const metaedit = app.plugins.plugins["metaedit"]?.api;
if (!metaedit) { dv.paragraph("❌ MetaEdit not loaded"); return; }

// specify the original file (not the currently open file)
const file = app.vault.getAbstractFileByPath("D&D/OneShot/Sniezynki/Subpages/Volatile Stats.md"); 
if (!file) { dv.paragraph("❌ File not found"); return; }

const root = this.container;

// Counters
const counters = [
  { field: "hp", label: "Hit Points", max: "hpmax" },
  { field: "temphp", label: "Temp HP" },
  { field: "hitdice", label: "Hit Dice", max: "hitdicemax" },
  { field: "dreadused", label: "Dread Used", max: "dreadusedmax" },
  { field: "spellslotsused", label: "Spell Slots Used", max: "spellslotsused_max" },
  { field: "magicalcunning", label: "Magical Cunning", max: "magicalcunning_max" },
  { field: "lucky", label: "Lucky", max: "luckymax" },
  { field: "raysickness", label: "Ray Sickness", max: "raysicknessmax" },
  { field: "holdperson", label: "Hold Person", max: "holdpersonmax" }
];

// Helper
async function getVal(f) {
  const v = await metaedit.getPropertyValue(f, file);
  return v == null ? 0 : Number(v);
}

// Counter row
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

  const updateDisplay = async () => {
    const cur = await getVal(cfg.field);
    const max = cfg.max ? await getVal(cfg.max) : null;
    valueEl.textContent = `${cur}${max !== null ? " / " + max : ""}`;
  };

  plus.onclick = async () => {
    let cur = await getVal(cfg.field);
    const max = cfg.max ? await getVal(cfg.max) : null;
    if (max !== null && cur >= max) return;
    await metaedit.update(cfg.field, cur + 1, file);
    await updateDisplay();
  };

  minus.onclick = async () => {
    let cur = await getVal(cfg.field);
    if (cur <= 0) return;
    await metaedit.update(cfg.field, cur - 1, file);
    await updateDisplay();
  };

  updateDisplay();
}

// Initialize table
const table = root.createEl("table");
table.classList.add("stat-table");

for (const cfg of counters) {
  makeCounter(cfg, table);
}

```