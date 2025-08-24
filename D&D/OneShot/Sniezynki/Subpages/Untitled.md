---
hp: 43
hpmax: 43
temphp: 16
hitdice: 6
hitdicemax: 6
dreadused: 1
dreadusedmax: 3
spellslotsused: 2
spellslotsused_max: 2
magicalcunning: 1
magicalcunning_max: 1
lucky: 0
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
const root = this.container; // stable reference

async function getVal(f) {
  const v = await metaedit.getPropertyValue(f, file);
  return v === null || v === undefined || v === "" ? 0 : Number(v);
}

async function setVal(f, v) {
  await metaedit.update(f, v, file);
  await render();
}

function makeCounter(field, label, maxField) {
  const row = root.createDiv({ cls: "flex gap-2 items-center my-1" });
  row.createSpan({ text: label + ":", cls: "font-bold" });
  const valueEl = row.createSpan({ text: "…" });

  const plus = row.createEl("button", { text: "➕" });
  const minus = row.createEl("button", { text: "➖" });

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
    valueEl.setText(` ${cur}${max !== null ? " / " + max : ""}`);
  };

  return row;
}

async function render() {
  root.innerHTML = "";

	/*const counters = [ 
	["hp", "Hit Points"], 
	["temphp", "Temp HP"], 
	["hitdice", "Hit Dice"], 
	["dreadused", "Dread Used"], 
	["spellslots_used", "Spell Slots Used"], 
	["magicalcunning", "Magical Cunning"], 
	["lucky", "Lucky"], 
	["raysickness", "Ray Sickness"], 
	["holdperson", "Hold Person"] ];

  for (const [field, label] of counters) { 
	  const row = makeCounter(field, label, field + "max", this.container); 
	  await row.refresh(); 
  }*/
  const row = makeCounter("hp", "Hit Points", "hpmax");
  await row.refresh();
  const row2 = makeCounter("lucky", "Hit Points", "luckymax");
  await row2.refresh();
}

render();

```
