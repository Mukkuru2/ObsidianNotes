<%*
const folder = "D&D/Wildemount/The Gold Prince/Inventory/Items";
const title = await tp.system.prompt("Itemnaam?");
if (!title) {
  tR += "No title given, aborting.";
  return; // stop here
}
const storedin = await tp.system.prompt("Stored in?") || "Backpack";

const content = `---
name: ${title}
quantity: 1
equipped: false
stored_in: ${storedin}
weight: 0
worth: 0
---`;

await tp.file.create_new(content, `${folder}/${title}`);
return;  // Stop template execution so no extra file is created
-%>
