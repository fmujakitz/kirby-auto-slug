# Kirby Auto Slug Field Plugin

This is a field plugin that automatically creates slug from defined fields.

Can be used to update page slug and title, client side.

****

## How to use

1. extract to `your-site/site/plugins/auto-slug`
2. set `type` field prop to `auto-slug`
3. set `uses` field prop to fields you want to use for slug creation
4. if you want to automatically update page slug, set `update-slug` field prop to `true`
5. if you want to generate and automatically update page title, set `shows` field prop to fields you want to use for title creation, and set `update-title` field prop to `true`



**Note:**
- To avoid possible conflicts use ie. [AutoID](https://github.com/bnomei/kirby3-autoid) as one of the fields.
- By setting `update-slug` and `update-title` page data is modified

## Basic usage
```
your-auto-slug-field:
  type: auto-slug
  label: Auto URL
  uses:
    - model
    - year
    
```
#### Result
```
  your-auto-slug-field:   model-2019
```

## Update page slug and title
```
your-field:
  type: auto-slug
  uses:
    model
    year
    autoid
  update-slug: true
  shows:
    make
    model
    year
  update-title: true
```

#### Result
```
page: {
  slug:   model-2019-zpxl0tjp
  title:  Make Model 2019
  content: {
    your-field: model-2019-zpxl0tjp
  }
}
```

## Todos
- randomness
- dx


## License

MIT

## Credits

- [Faris Mujakić](https://github.com/fmujakitz)
