<script setup>
import DatePicker from "../index.vue";
import "../dist/datepicker.min.css";
import { reactive } from "@vue/reactivity";

const state = reactive({
  date: "1399/12/16",
});
</script>

# Introduction

This persian / jalali datepicker package is implemented with vue 3, it's simple and light.

::: warning NOTICE
Works only on projects with version 3.x vue.
:::

All date manipulation and formatting are done via the [PersianDate](https://github.com/babakhani/PersianDate) library, so it's a direct dependency of this picker.

## Example

Choose a date

<DatePicker v-model="state.date" />

<br />
Datepicker comes with styling, but input itself does not. You can use `.datePicker` class, like:

```css
.datePicker input {
  /* add css style to the input */
}
```

## Installation

This section you can installing the package with npm or yarn.

**Npm**

```bash
$ npm install vue3-persian-datepicker
```

**Yarn**

```bash
$ yarn add vue3-persian-datepicker
```

Then import it in your code and use as a usual component:

```vue
<script>
import DatePicker from "vue3-persian-datepicker";
import "vue3-persian-datepicker/dist/datepicker.min.css";
import { reactive } from "@vue/reactivity";

export default {
  setup() {
    const state = reactive({
      date: "",
    });

    return {
      DatePicker,
      state,
    };
  },
};
</script>

<template>
  <DatePicker v-model="state.date" />
</template>
```

## Available props

| Prop        |   Type    |   Default    | Description                                                                                    |
| :---------- | :-------: | :----------: | :--------------------------------------------------------------------------------------------- |
| inline      | `Boolean` |              | If `true`, the datepicker is always displayed                                                  |
| format      | `String`  | `YYYY/MM/DD` | `PersianDate`-type format in which the string in the input should be both parsed and displayed |
| name        | `String`  |              | Input name property                                                                            |
| placeholder | `String`  |              | Input placeholder text                                                                         |

## Events

These events are emitted on actions in the datepicker

| Event    | Output | Description              |
| :------- | :----: | :----------------------- |
| opened   |        | The picker is opened     |
| closed   |        | The picker is closed     |
| selected | `Date` | A date has been selected |

## License

[MIT](https://opensource.org/licenses/MIT)
