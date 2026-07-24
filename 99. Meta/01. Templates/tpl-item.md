---
type: item
image: |-
  <%*
  const title = tp.file.title.replace(/ /g, "_");
  const url = `https://genshin-impact.fandom.com/wiki/${title}`;
  let result = "ImageNotFound";
  let attempts = 3;

  for (let i = 0; i < attempts; i++) {
    try {
      const response = await requestUrl({
        url: url,
        method: "GET",
        headers: {
          "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/150.0.0.0 Safari/537.36"
        }
      });
      const parsedHtml = new DOMParser().parseFromString(response.text, 'text/html');
      const img = parsedHtml.querySelector('img.pi-image-thumbnail');

      result = (img) ? img.getAttribute('src').split('/revision')[0] : "ImageNotFound";
    } catch (e) {
      if (i === attempts - 1) result = "FetchError";
    }
  }
  tR += result;
  %>
need: 0
---
# [<% tp.file.title %>](<<https://genshin-impact.fandom.com/wiki/><% tp.file.title %>>)

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
