---
type: item
image: https://static.wikia.nocookie.net/gensin-impact/images/a/a5/Item_Mist_Veiled_Lead_Elixir.png
need: 0
---
# [Mist Veiled Lead Elixir](<<https://genshin-impact.fandom.com/wiki/>Mist Veiled Lead Elixir>)

## Characters Requiring This

```js-engine
const mb = engine.getPlugin('obsidian-meta-bind-plugin')?.api;
const dvApi = app.plugins.getPlugin('dataview')?.api;
const combinedSumText = "***All Characters***";

if (!mb || !dvApi) {
  return engine.markdown.create('Missing Meta Bind or Dataview plugin.');
}

const itemName = context.file.basename;
const rows = dvApi.pages('"01. Characters"').array()
  .flatMap(page => page.file.lists.array()
    .filter(listItem => {
      return listItem.outlinks?.some(link => link.path === context.file.path);
    })
    .map(listItem => ({
      character: page.file.name,
      amount: Number(listItem.amt ?? 0),
    })));

// calculate total amount needed, then bind to frontmatter
const total = rows.reduce((sum, row) => sum + row.amount, 0);

// add sum to table
rows.push({ character: combinedSumText, amount: total });

// add square brackets around character names, but not for the total row
rows.forEach(row => {
  if (row.character !== combinedSumText) {
    row.character = `[[${row.character}]]`;
  }
});

const tableRows = rows.length > 0
  ? rows.map(row => `| ${row.character} | ${row.amount} |`).join('\n')
  : '| - | - |';

if (rows.length > 0) {
  const totalTarget = mb.createBindTarget('frontmatter', context.file.path, ['need']);
  
  // Delay the write slightly to prevent race conditions during file creation
  setTimeout(() => {
    mb.setMetadata(totalTarget, total);
  }, 100); 
}

return engine.markdown.create(`| Character(s) | Amount Needed |\n| --- | --- |\n${tableRows}`);
```

## Sources

### Bosses

### Crafting
