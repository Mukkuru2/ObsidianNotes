<%*
const folders = [
  "D&D/Wildemount/The Gold Price/Subpages/Inventory/Items",
  "D&D/OneShot/Sniezynki/Subpages/Inventory/Items",
  // Add more folders here
];

const title = await tp.system.prompt("Itemnaam?");
if (!title) {
  tR += "No title given, aborting.";
  return; // stop here
}

// Options for where the item is stored
const storageOptions = [
  "Backpack",
  "Miscelaneous Pouch",
  "Money Pouch",
  "Body"
  // Add more options here as needed
];

const storedin = await tp.system.suggester(storageOptions, storageOptions) || "Backpack";

const folder =  await tp.system.suggester(folders, folders);

const content = `---
name: ${title}
quantity: 1
equipped: false
stored_in: ${storedin}
weight: 0
worth: 0
---

#items
`;

await tp.file.create_new(content, `${folder}/${title}`);
return;  // Stop template execution so no extra file is created

-%>
