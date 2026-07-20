---
type: item
image: |-
  <%*
  const title = tp.file.title;
  const url = `https://genshin-impact.fandom.com/wiki/${title}`;
  try {
    const response = await requestUrl({ url });
    const parsedHtml = new DOMParser().parseFromString(response.text, 'text/html');
    const img = parsedHtml.querySelector('img.pi-image-thumbnail');
    console.log("IMG RESULT: ", img);
    tR += img ? img.getAttribute('src').split('/revision')[0] : "ImageNotFound";
  } catch (e) {
    tR += "FetchError";
  }
  %>
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
