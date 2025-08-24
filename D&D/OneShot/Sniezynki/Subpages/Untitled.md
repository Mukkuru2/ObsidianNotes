---
hpcurrent: 43
hpmax: 43

temphp: 12
temphpmax: 20

hitdice: 6
hitdicemax: 6

dread_used: 1
dread_used_max: 3

spellslots_used: 2
spellslots_used_max: 4

magical_cunning: 0
magical_cunning_max: 1

lucky: 0
lucky_max: 3

raysickness: 0
raysickness_max: 1

holdperson: 0
holdperson_max: 1
---



```dataviewjs
const metaedit = app.plugins.plugins["metaedit"]?.api;
if (!metaedit) {
  dv.paragraph("❌ MetaEdit not loaded");
  return;
}

const file = app.workspace.getActiveFile();

// Helpers
async function getVal(field) {
  return Number(await metaedit.getPropertyValue(field, file) ?? 0);
}
async function setVal(field, v) {
  await metaedit.update(field, String(v), file);
  await render();
}

// Make one counter row
async function makeCounter(field, label, maxField, parent) {
  const row = parent.createDiv({ cls: "flex gap-2 items-center my-1" });

  const name = row.createSpan({ text: label + ":" });
  name.style.minWidth = "160px";

  const valueEl = row.createSpan({ text: "…" });
  const plus = row.createEl("button", { text: "➕" });
  const minus = row.createEl("button", { text: "➖" });

  async function refresh() {
    const v = await getVal(field);
    const max = maxField ? await getVal(maxField) : null;
    valueEl.setText(` ${v}${max !== null ? " / " + max : ""}`);
  }

  plus.onclick = async () => {
    let v = await getVal(field);
    const max = maxField ? await getVal(maxField) : null;
    if (max !== null && v >= max) return;
    await setVal(field, v + 1);
  };

  minus.onclick = async () => {
    let v = await getVal(field);
    if (v <= 0) return;
    await setVal(field, v - 1);
  };

  row.refresh = refresh;
  return row;
}

// Render all counters
async function render() {
  this.container.innerHTML = "";

  const counters = [
    ["hpcurrent", "Hit Points", "hpmax"],
    ["temphp", "Temp HP", "temphpmax"],
    ["hitdice", "Hit Dice", "hitdicemax"],
    ["dread_used", "Dread Used", "dread_used_max"],
    ["spellslots_used", "Spell Slots Used", "spellslots_used_max"],
    ["magical_cunning", "Magical Cunning", "magical_cunning_max"],
    ["lucky", "Lucky", "lucky_max"],
    ["raysickness", "Ray Sickness", "raysickness_max"],
    ["holdperson", "Hold Person", "holdperson_max"],
  ];

  for (const [field, label, maxField] of counters) {
    const counter = await makeCounter(field, label, maxField, this.container);
    await counter.refresh();
  }
}

render();


```
