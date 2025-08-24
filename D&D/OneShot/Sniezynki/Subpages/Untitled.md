---
hpcurrent: 40
hpmax: 43
temphp: 16
hitdice: 6
hitdicemax: 6
dread_used: 1
dread_used_max: 3
spellslots_used: 2
spellslots_used_max: 2
magical_cunning: 1
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
const file = app.workspace.getActiveFile();

// read + write helpers
async function getVal(f) {
  return Number(await metaedit.getPropertyValue(f, file) ?? 0);
}
async function setVal(f, v) {
  await metaedit.update(f, String(v), file);
  await render();
}

// reusable counter row
async function makeCounter(field, label, maxField, parent) {
  const row = parent.createDiv({ cls: "flex gap-2 items-center my-1" });

  row.createSpan({ text: label + ":", cls: "font-bold" });
  const valueEl = row.createSpan({ text: "…" });

  const plus = row.createEl("button", { text: "➕" });
  const minus = row.createEl("button", { text: "➖" });

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

  row.refresh = async () => {
    const v = await getVal(field);
    const max = maxField ? await getVal(maxField) : null;
    valueEl.setText(` ${v}${max !== null ? " / " + max : ""}`);
  };
  return row;
}

// render all counters
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
    const row = await makeCounter(field, label, maxField, this.container);
    await row.refresh();
  }
}

render();


```
