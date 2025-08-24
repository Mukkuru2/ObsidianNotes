---
name: Sniezynki (Snie)
class: Warlock (The Undead)
level: 6
xp: 14000
race: Tiefling
background: Ice Demon
alignment: True Neutral
STR: 8
DEX: 10
CON: 14
INT: 10
WIS: 16
CHA: 18
proficiencies:
  - Arcana
  - Deception
  - Perception
  - Nature
save_proficiencies:
  - WIS
  - CHA
AC_base: 11
spellcasting_ability: CHA
hp: 43
temphp: 0
hitdice: 6
dreadused: 3
spellslotsused: 2
magicalcunning: 1
lucky: 3
raysickness: 1
holdperson: 1

---



--- start-multi-column: BaseStats  

| **Full Name**                  | `= this.name`        | **Level**             | `= this.level`                                                                    |
| ------------------------------ | -------------------- | --------------------- | --------------------------------------------------------------------------------- |
| **Background**                 | `= this.background ` | **Proficiency Bonus** | `= 2 + floor((this.level - 1) / 4)`                                               |
| **Alignment**                  | `= this.alignment`   | **HP**                | `= [[Volatile Stats]].hpcurrent` + `= [[Volatile Stats]].temphp` / `=this.HP_max` |
| **Class**                      | `= this.class`       | **Hit Dice**          | `= [[Volatile Stats]].hitdice` / `= this.level`                                   |
| **Xp**                         | `= this.xp`          |                       |                                                                                   |





```dataviewjs
  const stats = ["STR", "DEX", "CON", "INT", "WIS", "CHA"];
  const pb = 2 + Math.floor((dv.current().level - 1) / 4);
  const saves = dv.current().save_proficiencies;

  let combinedTable = [];

  for (let stat of stats) {
  const val = dv.current()[stat];
  const mod = Math.floor((val - 10) / 2);
  const isProf = saves.includes(stat);
  const save = mod + (isProf ? pb : 0);

    combinedTable.push([
    stat,
    val,
    `${mod >= 0 ? "+" : ""}${mod}`,
    isProf ? "✔️" : "",
    `${save >= 0 ? "+" : ""}${save}`
    ]);
  }

  dv.table(["Stat", "Score", "Modifier", "Save Prof?", "Save Total"], combinedTable);
```


--- column-end ---

```dataviewjs
const pb = 2 + Math.floor((dv.current().level - 1) / 4);
const allSkills = {
  "Athletics": "STR",
  "Acrobatics": "DEX",
  "Sleight of Hand": "DEX",
  "Stealth": "DEX",
  "Arcana": "INT",
  "History": "INT",
  "Investigation": "INT",
  "Nature": "INT",
  "Religion": "INT",
  "Animal Handling": "WIS",
  "Insight": "WIS",
  "Medicine": "WIS",
  "Perception": "WIS",
  "Survival": "WIS",
  "Deception": "CHA",
  "Intimidation": "CHA",
  "Performance": "CHA",
  "Persuasion": "CHA"
};

const skillProfs = dv.current().proficiencies;
let skillTable = [];

for (let [skill, stat] of Object.entries(allSkills)) {
  const mod = Math.floor((dv.current()[stat] - 10) / 2);
  const isProf = skillProfs.includes(skill);
  const total = mod + (isProf ? pb : 0);
  skillTable.push([
    skill,
    stat,
    isProf ? "✔️" : "",
    `${total >= 0 ? "+" : ""}${total}`
  ]);
}

dv.table(["Skill", "Stat", "Proficient", "Total"], skillTable);
```


--- end-multi-column


#### Abilities


```dataviewjs
const file = app.workspace.getActiveFile(); // or specify another file via path
const root = this.container;

// Stat definitions
const counters = [
  { field: "hp", label: "Hit Points", max: 43 },
  { field: "temphp", label: "Temp HP" }, // no max
  { field: "hitdice", label: "Hit Dice", max: this.level },
  { field: "dreadused", label: "Dread Used", max: 3 },
  { field: "spellslotsused", label: "Spell Slots Used", max: 2 },
  { field: "magicalcunning", label: "Magical Cunning", max: 1 },
  { field: "lucky", label: "Lucky", max: 3 },
  { field: "raysickness", label: "Ray Sickness", max: 1 },
  { field: "holdperson", label: "Hold Person", max: 1 }
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

