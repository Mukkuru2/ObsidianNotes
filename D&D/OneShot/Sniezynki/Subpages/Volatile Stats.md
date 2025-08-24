---
hpcurrent: 43
temphp: 12
hitdice: 6

dread_used: 1
spellslots_used: 2
magical_cunning: 0
lucky: 0
raysickness: 0
holdperson: 0
---

```dataviewjs
// Grab MetaEdit + Buttons APIs
const { update, getPropertyValue } = app.plugins.plugins["metaedit"].api;
const { createButton } = app.plugins.plugins["buttons"];

// Current file path
const filePath = dv.current().file.path;

// Read current HP (defaults to 0 if missing)
const current = Number(await getPropertyValue("hpcurrent", filePath) ?? 0);
dv.paragraph(`**HP:** ${current}`);

// Helper to change HP by delta
const bump = async (delta) => {
  const v = Number(await getPropertyValue("hpcurrent", filePath) ?? 0);
  await update("hpcurrent", String(v + delta), filePath);
};

// Render + and - buttons
createButton({
  app, el: this.container,
  args: { name: "➕ HP" },
  clickOverride: { click: () => bump(1) }
});

createButton({
  app, el: this.container,
  args: { name: "➖ HP" },
  clickOverride: { click: () => bump(-1) }
});
```
