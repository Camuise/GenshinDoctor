---
type: item
image: https://static.wikia.nocookie.net/gensin-impact/images/7/73/Item_Mist_Grass.png
need: 5.345342534555345e+46
---
# [Mist Grass](<<https://genshin-impact.fandom.com/wiki/>Mist Grass>)

## Characters Requiring This

```js-engine
const mb = engine.getPlugin('obsidian-meta-bind-plugin')?.api;
const dvApi = app.plugins.getPlugin('dataview')?.api;
const combinedSumText = "***All Characters***";

if (!mb || !dvApi) {
  return engine.markdown.create('> [!missing] Missing Meta Bind or Dataview plugin.');
}

// the actual logic for finding characters that need this item
const rows = dvApi.pages('"01. Characters"').array()
  .flatMap(page => page.file.lists.array()
    // collect only list items that link to this items
    .filter(listItem => {
      return listItem.outlinks?.some(link => link.path === context.file.basename);
    })
    // map to a simpler object with character name and amount
    .map(listItem => ({
      character: page.file.name,
      amount: Number(listItem.amt ?? 0),
    }))
    // deduplicate rows w/ same character name by summing amounts
    .reduce((acc, curr) => {
      const existing = acc.find(row => row.character === curr.character);
      if (existing) {
        existing.amount += curr.amount;
      } else {
        acc.push(curr);
      }
      return acc;
    }, [])
  );
// total sum
const total = rows.reduce((sum, row) => sum + row.amount, 0);
rows.push({ character: combinedSumText, amount: total });

// bind to frontmatter (gah)
if (rows.length > 0) {
  const totalTarget = mb.createBindTarget('frontmatter', context.file.path, ['need']);
  mb.setMetadata(totalTarget, total);
}

// generate the table. no need to verify length since we always have the total row
const tableRows = rows.map(row => { 
  const char = row.character;
  if (char !== combinedSumText) {
    // add image and square brackets around character name
    const charPage = dvApi.pages(`"/01. Characters/${char}.md"`);
    const imageLinkFromPage = charPage.file.frontmatter?.image;
    const imageMarkdown = imageLinkFromPage ? `![${char}](${imageLinkFromPage}) ` : '';
    row.character = `${imageMarkdown}[[${char}]]`;
  };
  return `| ${char} | ${row.amount} |`; 
}).join('\n')
return engine.markdown.create(`| Character(s) | Amount Needed |\n| --- | --- |\n${tableRows}`);
```

## Sources

### Bosses

### Crafting
