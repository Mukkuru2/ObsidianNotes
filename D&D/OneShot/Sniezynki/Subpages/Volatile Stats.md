---
hp: 41
hpmax: 43
temphp: 0
hitdice: 5
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
const file = app.workspace.getActiveFile(); // or specify another file via path
const root = this.container;

// Stat definitions
const counters = [
  { field: "hp", label: "Hit Points", max: "hpmax" },
  { field: "temphp", label: "Temp HP" }, // no max
  { field: "hitdice", label: "Hit Dice", max: "hitdicemax" },
  { field: "dreadused", label: "Dread Used", max: "dreadusedmax" },
  { field: "spellslotsused", label: "Spell Slots Used", max: "spellslotsusedmax" },
  { field: "magicalcunning", label: "Magical Cunning", max: "magicalcunningmax" },
  { field: "lucky", label: "Lucky", max: "luckymax" },
  { field: "raysickness", label: "Ray Sickness", max: "raysicknessmax" },
  { field: "holdperson", label: "Hold Person", max: "holdpersonmax" }
];

// Helper to read current value from YAML
async function getVal(field) {
  const content = await app.vault.read(file);
  const match = content.match(new RegExp(`^${field}:\\s*(\\d+)`, 'm'));
  return match ? Number(match[1]) : 0;
}

// Helper to set value directly in YAML
async function setValDirect(field, newVal) {
  let content = await app.vault.read(file);
  const regex = new RegExp(`^${field}:\\s*(\\d+)`, 'm');
  if (content.match(regex)) {
    content = content.replace(regex, `${field}: ${newVal}`);
  } else {
    content += `\n${field}: ${newVal}`;
  }
  await app.vault.modify(file, content);
}

// Create a table row with buttons
function makeCounter(cfg, table) {
  const row = table.insertRow();

  // Label
  row.insertCell().textContent = cfg.label + ":";

  // Value display
  const valueCell = row.insertCell();
  const valueEl = valueCell.appendChild(document.createElement("span"));

  // Buttons
  const btnCell = row.insertCell();
  const minus = btnCell.appendChild(document.createElement("button"));
  minus.textContent = "➖";
  const plus = btnCell.appendChild(document.createElement("button"));
  plus.textContent = "➕";

  // Update display text
  const updateDisplay = async () => {
    const cur = await getVal(cfg.field);
    const max = cfg.max ? await getVal(cfg.max) : null;
    valueEl.textContent = `${cur}${max !== null ? " / " + max : ""}`;
  };

  // Button actions
  plus.onclick = async () => {
    let cur = await getVal(cfg.field);
    const max = cfg.max ? await getVal(cfg.max) : null;
    if (max !== null && cur >= max) return;
    await setValDirect(cfg.field, cur + 1);
    await updateDisplay();
  };

  minus.onclick = async () => {
    let cur = await getVal(cfg.field);
    if (cur <= 0) return;
    await setValDirect(cfg.field, cur - 1);
    await updateDisplay();
  };

  // Initial display
  updateDisplay();
}

// Initialize table
const table = root.createEl("table");
table.classList.add("stat-table");

for (const cfg of counters) {
  makeCounter(cfg, table);
}


```