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
HP_max: 43
spellcasting_ability: CHA
---

--- start-multi-column: BaseStats  

| **Full Name**  | `= this.name`     | **Level** | `= this.level` | 
| -------------- | ------------------- | ---| ---|
| **Background** | `= this.background `             | **Proficiency Bonus** | `= 2 + floor((this.level - 1) / 4)` |
| **Alignment**  | `= this.alignment`      | **HP** | `= [[Volatile Stats]].hpcurrent` + `= [[Volatile Stats]].temphp` / `=this.HP_max` |
| **Class**      | `= this.class`             | **Hit Dice** | `= [[Volatile Stats]].hitdice` / `= this.level` |
| **Xp**         | `= this.xp`                | 





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

#### Abilities

| **Ability**         | **Left**                                                               |
| ------------------- | ---------------------------------------------------------------------- |
| **Spellslots**      | `=2 - [[Volatile Stats]].spellslots_used` / 2                              |
| **Dread form**      | `=(2 + floor((this.level - 1) / 4)) - [[Volatile Stats]].dread_used` / `= 2 + floor((this.level - 1) / 4)` |
| **Magical Cunning** | `=1 - [[Volatile Stats]].spellslots_used` / 1                              |


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
