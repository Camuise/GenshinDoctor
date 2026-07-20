---
type: character
image: |-
  <%*
  const title = tp.file.title.replace(/ /g, "_");
  const url = `https://genshin-impact.fandom.com/wiki/${title}/Gallery`;
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
      const img = parsedHtml.querySelector('.wikia-gallery-item .thumb a.image img');
      
      result = (img) ? img.getAttribute('src').split('/revision')[0] : "ImageNotFound";
    } catch (e) {
      if (i === attempts - 1) result = "FetchError";
    }
  }
  tR += result;
  %>
---
# [<% tp.file.title %>](<<https://genshin-impact.fandom.com/wiki/><% tp.file.title %>>)

## Character Level

### Lv ->

- Item::

## Weapon
