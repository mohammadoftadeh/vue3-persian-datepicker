<template>
  <div class="datePicker">
    <input
      type="text"
      :name="name"
      :placeholder="placeholder"
      v-model="state.date"
      @input="datePickerHandleChange"
      @focus="datePickerHandleShow(true, 'opened')"
    />
    <section v-if="inline || state.showDatePicker" class="datePicker__section">
      <table>
        <div v-if="state.showMonthList" class="datePicker__div">
          <button
            type="button"
            v-for="(month, index) in state.rangeNameMonths"
            :key="index"
            :class="{ 'datePicker--active': state.month === ++index }"
            @click="chooseMonth(index)"
          >
            {{ month }}
          </button>
        </div>
        <div
          :class="[
            'datePicker__div',
            !state.showYearList && 'datePicker__div--invisible',
          ]"
        >
          <button
            type="button"
            v-for="(year, index) in state.years"
            :key="index"
            :class="{ 'datePicker--active': state.year === year }"
            :data-scroll="state.year === year ? 1 : ''"
            @click="chooseYear(year)"
          >
            {{ year }}
          </button>
        </div>
        <thead>
          <tr>
            <th class="datePicker__th__button">
              <button type="button" class="" @click="prevMonthHandle">
                ماه قبل
              </button>
            </th>
            <th class="datePicker__th__button">
              <button type="button" @click="state.showMonthList = true">
                {{ state.monthName(state.year, state.month) }}
              </button>
            </th>
            <th>
              <button type="button" @click="openYearList">
                {{ state.year }}
              </button>
            </th>
            <th class="datePicker__th__button">
              <button type="button" @click="nextMonthHandle">ماه بعد</button>
            </th>
          </tr>
          <tr>
            <th v-for="(item, index) in state.weekdaysMin" :key="index">
              {{ item }}
            </th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="(item, index) in state.calcNumberWeeks(
              state.offsetDay(state.firstDayMonth(state.year, state.month)),
              state.lastOffsetDay(
                state.lastDayMonth(
                  state.year,
                  state.month,
                  state.daysInMonth(state.year, state.month)
                )
              ),
              state.daysInMonth(state.year, state.month)
            )"
            :key="item"
          >
            <td
              v-for="(day, idx) in state.tableDays(
                state.daysInMonth(state.year, state.month),
                state.prevMonthDays(
                  state.prevMonth(state.year, state.month),
                  state.offsetDay(state.firstDayMonth(state.year, state.month))
                ),
                state.nextMonthDays(
                  state.lastOffsetDay(
                    state.lastDayMonth(
                      state.year,
                      state.month,
                      state.daysInMonth(state.year, state.month)
                    )
                  )
                )
              )[index]"
              :key="idx"
              :class="{
                'datePicker__td--disable': typeof day !== 'number',
                'datePicker__td--active': state.selected === day,
                'datePicker--active':
                  !state.selected &&
                  state.today === day &&
                  state.checkYearMonth(state.year, state.month),
                'datePicker--highlight':
                  state.today === day &&
                  state.checkYearMonth(state.year, state.month),
              }"
              @click="dateSelectedHandle($event, day)"
            >
              {{ day }}
            </td>
          </tr>
          <tr class="mt-3 mb-2 mx-1">
            <button type="button" class="datePicker__button" @click="goToday">
              امروز
            </button>
          </tr>
        </tbody>
      </table>
    </section>
  </div>
</template>

<script>
import PersianDate from "persian-date";
import { computed, reactive } from "vue";

export default {
  name: "DatePicker",
  props: ["name", "inline", "format", "placeholder"],
  setup(props, context) {
    const jalal = new PersianDate();

    const state = reactive({
      year: jalal.year(),
      daysInMonth: computed(() => {
        return (year, month) => new PersianDate([year, month]).daysInMonth();
      }),
      prevMonth: computed(() => {
        return (year, month) =>
          new PersianDate([year, month <= 1 ? 1 : month - 1]).daysInMonth();
      }),
      nextMonth: computed(() => {
        return (year, month) =>
          new PersianDate([year, month >= 12 ? 1 : month + 1]).daysInMonth();
      }),
      weekdaysMin: jalal.rangeName().weekdaysMin,
      months: jalal.rangeName().months,
      month: jalal.month(),
      today: jalal.date(),
      firstDayMonth: computed(() => {
        return (year, month) =>
          new PersianDate([year, month, 1]).format("dddd");
      }),
      monthName: computed(() => {
        return (year, month) => new PersianDate([year, month]).format("MMMM");
      }),
      offsetDay: computed(() => {
        return (firstDayMonth) => {
          switch (firstDayMonth) {
            case "یکشنبه":
              return 1;
            case "دوشنبه":
              return 2;
            case "سه شنبه":
              return 3;
            case "چهار شنبه":
              return 4;
            case "پنج‌شنبه":
              return 5;
            case "جمعه":
              return 6;
            default:
              return 0;
          }
        };
      }),
      lastDayMonth: computed(() => {
        return (year, month, daysInMonth) =>
          new PersianDate([year, month, daysInMonth]).format("dddd");
      }),
      lastOffsetDay: computed(() => {
        return (lastDayMonth) => {
          switch (lastDayMonth) {
            case "یکشنبه":
              return 5;
            case "دوشنبه":
              return 4;
            case "سه شنبه":
              return 3;
            case "چهار شنبه":
              return 2;
            case "پنج‌شنبه":
              return 1;
            case "شنبه":
              return 6;
            default:
              return 0;
          }
        };
      }),
      prevMonthDays: computed(() => {
        return (prevMonth, offset) => {
          let days = [];
          for (let i = prevMonth; i > prevMonth - offset; i--) {
            days.push(i);
          }
          return days.reverse();
        };
      }),
      nextMonthDays: computed(() => {
        return (offset) => {
          let days = [];
          for (let i = 1; i < offset + 1; i++) {
            days.push(i);
          }
          return days;
        };
      }),
      rangeNameMonths: jalal.rangeName().months,
      years: computed(() => {
        let years = [];
        let start = 1300;
        while (jalal.year() + 20 >= start) {
          years.push(start++);
        }

        return years;
      }),
      selected: null,
      checkYearMonth: computed(() => {
        return (year, month) => {
          return jalal.year() === year && jalal.month() === month;
        };
      }),
      calcNumberWeeks: computed(
        () => (offsetDay, lastOffsetDay, daysInMonth) => {
          return Math.floor((offsetDay + lastOffsetDay + daysInMonth) / 7);
        }
      ),
      tableDays: computed(() => (daysInMonth, prevMonthDays, nextMonthDays) => {
        return genMatrix(daysInMonth, 7, prevMonthDays, nextMonthDays);
      }),
      showYearList: false,
      showMonthList: false,
      showDatePicker: false,
      date: `${jalal.year()}/${jalal.month()}/${jalal.date()}`,
    });

    const prevMonthHandle = () => {
      if (state.month <= 1) {
        state.month = 12;
        state.year -= 1;
        return;
      }

      state.month -= 1;
    };

    const nextMonthHandle = () => {
      if (state.month >= 12) {
        state.month = 1;
        state.year += 1;
        return;
      }
      state.month += 1;
    };

    const dateSelectedHandle = (event = null, index) => {
      if (
        event &&
        event.target.classList["value"].indexOf("datePicker__td--disable") > -1
      )
        return;

      state.selected = index;
      state.date = new PersianDate([state.year, state.month, index])
        .toLocale("en")
        .format(props.format || "YYYY/MM/DD");
      datePickerHandleShow(false, "closed");
      context.emit("update:modelValue", state.date);
      context.emit("selected", state.date);
    };

    const goToday = () => {
      state.year = jalal.year();
      state.month = jalal.month();
      state.today = jalal.date();
      state.selected = state.today;
      dateSelectedHandle(null, state.today);
    };

    const genMatrix = (n, length, firstItems, lastItems) => {
      let result = [];
      let genArrNum = [...firstItems.map((item) => item.toString())];
      for (let i = 1; i <= n; i++) {
        genArrNum.push(i);
      }

      genArrNum = [...genArrNum, ...lastItems.map((item) => item.toString())];

      for (let i = 0; i < genArrNum.length / length; i++) {
        result.push(genArrNum.slice(i * length, i * length + length));
      }

      return result;
    };

    const chooseMonth = (month) => {
      state.month = month;
      state.showMonthList = false;
    };

    const chooseYear = (year) => {
      state.year = year;
      state.showYearList = false;
    };

    const openYearList = () => {
      state.showYearList = true;
      document.querySelector("button[data-scroll='1']").scrollIntoView();
    };

    const datePickerHandleChange = (event) => {
      const dateArr = event.target.value.split("/");
      if (Number(dateArr[1]) > 12) return;

      state.year = Number(dateArr[0]);
      state.month = Number(dateArr[1]);
      state.today = Number(dateArr[2]);
      state.selected = state.today;
      context.emit("update:modelValue", event.target.value);
    };

    const datePickerHandleShow = (flag, emit) => {
      state.showDatePicker = flag;
      context.emit(emit);
    };

    document.onclick = function (event) {
      if (
        document.querySelector(".datePicker").contains(event.target) ||
        (event.target.parentNode.classList &&
          event.target.parentNode.classList["value"].indexOf(
            "datePicker__div"
          ) > -1)
      ) {
        return;
      }

      if (state.showDatePicker) {
        datePickerHandleShow(false, "closed");
        state.showYearList = false;
        state.showMonthList = false;
      }
    };

    return {
      state,
      prevMonthHandle,
      nextMonthHandle,
      dateSelectedHandle,
      goToday,
      chooseMonth,
      chooseYear,
      openYearList,
      datePickerHandleChange,
      datePickerHandleShow,
    };
  },
};
</script>
