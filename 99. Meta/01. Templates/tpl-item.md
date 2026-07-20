---
type: item
---
# [<% tp.file.title %>](https://genshin-impact.fandom.com/wiki/<% tp.file.title %>)

## Characters Requiring This
```dataview
TABLE Item as "Quantity Needed"
FROM #character
WHERE Item != null AND contains(any(Item), this.file.link)
```

## Sources
### Bosses

### Crafting