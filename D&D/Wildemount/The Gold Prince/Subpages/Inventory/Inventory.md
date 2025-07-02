---
p_treasury:
  cp: 23
  sp: 143
  gp: 221
  pp: 6

team_treasury:
  cp: 0
  sp: 8
  gp: 493
  pp: 0


---

```dataviewjs
function calcTreasury(t) {
  const values = { cp: 0.01, sp: 0.1, gp: 1, pp: 10 };
  let total = 0;
  for (let coin in values) {
    total += (t[coin] ?? 0) * values[coin];
  }
  return total.toFixed(2);
}

const self = dv.current().p_treasury ?? {};
const team = dv.current().team_treasury ?? {};

dv.header(3, "💰 Treasury");

dv.table(
  ["Source", "CP", "SP", "GP", "PP", "Total (gp)"],
  [
    ["You", self.cp ?? 0, self.sp ?? 0, self.gp ?? 0, self.pp ?? 0, `${calcTreasury(self)} gp`],
    ["Team", team.cp ?? 0, team.sp ?? 0, team.gp ?? 0, team.pp ?? 0, `${calcTreasury(team)} gp`]
  ]
);

```


```dataviewjs
const folder = dv.current().file.folder + "/Items";
const items = dv.pages('"' + folder + '"')
  .where(p => p.quantity !== undefined)
  .sort(p => p.stored_in ?? "");

// Calculate totals
let totalWeight = 0;
let totalWorth = 0;

let groups = new Map();
for (let p of items) {
  const loc = p.stored_in ?? "Onbekend";
  if (!groups.has(loc)) groups.set(loc, []);
  groups.get(loc).push(p);
}

const allLocations = Array.from(groups.keys());
const half = Math.ceil(allLocations.length / 2);
const leftLocs = allLocations.slice(0, half);
const rightLocs = allLocations.slice(half);

function buildRows(locations) {
  let rows = [];
  for (let loc of locations) {
    rows.push({isCategory: true, label: `📦 ${loc}`});
    for (let p of groups.get(loc)) {
      rows.push({
        isCategory: false,
        item: p.file.link,
        qty: p.quantity ?? 1,
        weight: p.weight ?? 0,
        worth: p.worth ?? 0
      });
    }
  }
  return rows;
}

const leftRows = buildRows(leftLocs);
const rightRows = buildRows(rightLocs);

// Get max length for pairing rows
const maxRows = Math.max(leftRows.length, rightRows.length);

let finalRows = [];
for (let i = 0; i < maxRows; i++) {
  const left = leftRows[i] || null;
  const right = rightRows[i] || null;

  finalRows.push([
    // Left side columns
    left
      ? left.isCategory
        ? left.label
        : left.item
      : "",
    left && !left.isCategory ? left.qty : "",
    left && !left.isCategory ? left.weight : "",
    left && !left.isCategory ? left.worth : "",

    // Right side columns
    right
      ? right.isCategory
        ? right.label
        : right.item
      : "",
    right && !right.isCategory ? right.qty : "",
    right && !right.isCategory ? right.weight : "",
    right && !right.isCategory ? right.worth : "",
  ]);
}


for (let p of items) {
  totalWeight += p.weight * p.quantity ?? 0;
  totalWorth += p.worth * p.quantity ?? 0;
}

finalRows.push(["\n\n<nbsp>\n<nbsp>\n","","","","","","",""])

// Add a totals row to finalRows
finalRows.push([
  "Totals", "", totalWeight, totalWorth, // Left side totals
  "", "", "", ""                          // Right side empty
]);

// Render the table with totals
dv.table(
  [
    "Item", "Qty", "Weight", "Worth",
    "Item", "Qty", "Weight", "Worth",
  ],
  finalRows
);

```
