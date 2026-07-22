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
