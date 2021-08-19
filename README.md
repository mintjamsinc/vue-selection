<img src="https://www.mintjams.jp/img/cr.svg" alt ="" width="64">

# vue-selection
A reusable Selection component for Vue.js 2.x used by webtop applications.

## Installation

```sh
npm install --save-dev @mintjamsinc/vue-selection
```

## Usage

```vue
<Selection
  ref="tags"
  :multiple="true"
  maxLabelWidth="10em"
  v-hook="{inserted: onTagsLoad}"/>
```

```js
import Selection from '@mintjamsinc/vue-selection';

export default {
  components: {
    Selection,
  },
  methods: {
    onTagsLoad() {
      // let ui = vm.$refs.tags.ui;
      // ui.getIdentifier = function(o) {};
      // ui.getAuthorizable = function(o) {};
      // ui.getItem = function(o) {};
      // ui.getIcon = function(o) {};
      // ui.getLabel = function(o) {};
      // ui.comparator = function(a, b) {};
      // ui.onChanged = function() {};
      // ui.objects = ...
      // ui.selection = ...
    },
  },
}
```

## License

[MIT](https://opensource.org/licenses/MIT)

Copyright (c) 2021 MintJams Inc.