<br />

[![npm](https://img.shields.io/npm/v/vue3-persian-datepicker)](https://www.npmjs.com/package/vue3-persian-datepicker)

<iframe
  title="star repo"
  src="https://ghbtns.com/github-btn.html?user=mohammadoftadeh&repo=vue3-persian-datepicker&type=star"
  frameworker="0"
  scrolling="0"
  width="75px"
  height="25px"
  frameBorder="none"
></iframe>

# Introduction

This persian / jalali datepicker package is implemented with vue 3, it's simple and light.

::: warning NOTICE
Works only on projects with version 3.x vue.
:::

All date manipulation and formatting are done via the [PersianDate](https://github.com/babakhani/PersianDate) library, so it's a direct dependency of this picker.

## Example

CodeSandBox Demo, Choose a date:

<iframe src="https://codesandbox.io/embed/blissful-shirley-pyqiw?autoresize=1&fontsize=14&hidenavigation=0&theme=dark&view=preview"
     style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
     title="vue3-persian-datepicker"
     allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
     sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
   ></iframe>

<br />
<br />

Datepicker comes with less styling, but input itself does not. You can use `.datePicker` class for styling input and all elements, like:

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

::: warning NOTICE
If you use default value for `v-model`, You should act like the following format:
:::

```js
const state = reactive({ date: "1399/12/16" // for example })
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
