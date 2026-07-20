---
type: character
image: |-
  <%*
  const title = tp.file.title;
  const url = `https://genshin-impact.fandom.com/wiki/${title}/Gallery`;
  try {
    const response = await requestUrl({ url });
    const parsedHtml = new DOMParser().parseFromString(response.text, 'text/html');
    const img = parsedHtml.querySelector('.wikia-gallery-item .thumb a.image img');
    tR += img ? img.getAttribute('src').split('/revision')[0] : "ImageNotFound";
  } catch (e) {
    tR += "FetchError";
  }
  %>
---
# [<% tp.file.title %>](<https://genshin-impact.fandom.com/wiki/<% tp.file.title %>>)

## Character Level

### Lv ->

- Item::

## Weapon
