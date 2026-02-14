---
name: The Golden Prince (Goldie)
class: Fighter (Eldritch Knight)
level: 4
race: Dragonborn
background: Prince
alignment: Lawful Neutral
STR: 18
DEX: 10
CON: 16
INT: 16
WIS: 14
CHA: 14
proficiencies:
  - Athletics
  - Arcana
  - History
  - Insight
  - Performance
  - Pursuasion
save_proficiencies:
  - STR
  - CON
AC_base: 16
HP_max: 37
HP_current: 37
HP_temp: 5
Initiative_bonus: 0
spellcasting_ability: INT
---

--- start-multi-column: BaseStats  

| **Full Name**  | `= this.name`     |  |  | 
| -------------- | ------------------- | ---| ---|
| **Background** | `= this.background `             | **Proficiency Bonus** | `= 2 + floor((this.level - 1) / 4)` |
| **Alignment**  | `= this.alignment`      | Hit Dice Total | `=this.level` |
| **Class**      | `= this.class`             | Max HP |  `=this.HP_max`|
| **Subclass**   | [Eldritch Knight]() | HP |  `=this.HP_current`|
| **Xp**         | 2262                |



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
