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
total needed: "`= sum(pages('#character').where(p => contains(p.Item, this.file.link)).Item)`"
---
# [<% tp.file.title %>](<<https://genshin-impact.fandom.com/wiki/><% tp.file.title %>>)

## Characters Requiring This

```dataview
TABLE Item as "Quantity Needed"
FROM #character
WHERE Item != null AND contains(any(Item), this.file.link)
```

## Sources

### Bosses

### Crafting
