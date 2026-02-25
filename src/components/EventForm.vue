<template>
  <div>
    <!-- event-form -->
    <div class="form-field form-field--with-icon">
      <svg class="form-field__icon" viewBox="0 0 16 16" xmlns="http://www.w3.org/2000/svg">
        <circle cx="8" cy="8" r="6.5" fill="#d4a574" opacity="0.5" />
        <circle cx="11.5" cy="6.5" r="5.5" fill="#0a0e27" />
      </svg>
      <input type="text" class="form-field__input form-field__input--icon" id="lunarEventName"
        placeholder="음력일정명"
        v-model="lunarEventName"
        v-on:change="eventGenerate">
    </div>
    <div class="form-field">
      <input type="date" class="form-field__input" id="lunarEventDate"
        v-model="lunarEventDate"
        v-on:change="eventGenerate">
      <div class="form-field__hint">
        양력기준: {{ solarEventDate }}
        <span v-show="isYun">, {{ solarYunEventDate }} <span class="badge--yun" v-show="isYun">윤</span></span>
      </div>
    </div>
    <div class="actions">
      <a class="btn--primary"
          v-bind:class="{ 'btn--disabled': events.length === 0 }"
          v-bind:href="icsContent"
          v-bind:download="filename">Calendar 등록</a>
      <button class="btn--ghost" v-on:click="resetEvent">리셋</button>
    </div>
    <!-- // event-form -->
    <!-- event-list -->
    <ul class="event-list" v-if="events.length">
      <li class="event-list__item"
        v-for="(event, index) in events"
        v-bind:key="index"
        :style="{ animationDelay: index * 40 + 'ms' }">
          <span class="event-list__date">양력: {{ event.date }}</span>
          <span class="event-list__badges">
            <span class="badge--dow">{{ event.dayOfWeek }}요일</span>
            <span class="badge--yun" v-show="event.isYun">윤</span>
          </span>
      </li>
    </ul>
    <!-- // event-list -->
  </div>
</template>

<script>
import holidayKR from 'holiday-kr';
import * as ics from 'ics';
import { faker } from '@faker-js/faker';

var today = new Date();

export default {
  data: function() {
    return {
      'lunarEventName'  : '',
      'lunarEventDate'  : [today.getFullYear(), this.padder(today.getMonth() - 1), '01'].join('-'),
      'events'          : [],
      'icsEvents'       : [],
      'isYun'           : false
    }
  },
  computed: {
    icsContent: function() {
      const { error, value } = ics.createEvents(this.icsEvents);
      if (error) { return '' }
      return 'data:text/calendar;charset=utf-8,' + encodeURIComponent(value);
    },
    solarEventDate: function() {
      let [year, month, day] = this.lunarEventDate.split('-');
      if (year < 1900) { return '' }
      try {
        var gregorian  = holidayKR.getSolar(year, month, day, false);
      }
      catch(err) {
        return 'N/A';
      }
      return [gregorian.year, this.padder(gregorian.month), this.padder(gregorian.day)].join('-');
    },
    solarYunEventDate: function() {
      let [year, month, day] = this.lunarEventDate.split('-');
      if (year < 1900) { return '' }
      try {
        var gregorianY  = holidayKR.getSolar(year, month, day, true);
      }
      catch(err) {
        return 'N/A';
      }
      return [gregorianY.year, this.padder(gregorianY.month), this.padder(gregorianY.day)].join('-');
    },
    hasYun: function() {
      let [year, month, day] = this.lunarEventDate.split('-');
      if (year < 1900) { return false }
      try {
        holidayKR.getSolar(year, month, day, true);
      }
      catch(err) {
        return false;
      }
      return true;
    },
    filename: function() {
      var fakers = [
        faker.color.human(),
        faker.commerce.product(),
        'in',
        faker.hacker.noun()
      ].join('-').replace(/ /g, '-').toLowerCase();
      return fakers + '.ics';
    }
  },
  watch: {
    hasYun: function(val) {
      this.isYun = val;
    }
  },
  methods: {
    eventGenerate: function() {
      this.events = [];
      this.icsEvents = [];
      var [year, month, day] = this.lunarEventDate.split('-');
      if (year < 1900) { return '' }
      for (var i = year; i <= 2050; i++) {
        try {
          var gregorian = holidayKR.getSolar(i, month, day, false);
        }
        catch(err) {
          gregorian = null;
        }
        try {
          var gregorianY = holidayKR.getSolar(i, month, day, true);
        }
        catch(err) {
          gregorianY = null;
        }
        if (gregorian) { this.addEvent(gregorian, false) }
        if (gregorianY) { this.addEvent(gregorianY, true) }
      }
    },
    resetEvent: function() {
      this.events = [];
      this.icsEvents = [];
      this.lunarEventDate = [today.getFullYear(), this.padder(today.getMonth() - 1), '01'].join('-'),
      this.lunarEventName = '';
    },
    padder: function(integer) {
      var str = "" + integer;
      var pad = "00";
      return (pad + str).slice(-pad.length);
    },
    addEvent: function(gregorian, isYun) {
      var [year, month, day] = this.lunarEventDate.split('-');
      var title = this.lunarEventName + ' (음력: ' + [month, day].join('-') + ')';
      if (isYun) { title += ' (윤)'}
      var event = {
        date: [gregorian.year, this.padder(gregorian.month), this.padder(gregorian.day)].join('-'),
        year: gregorian.year,
        month: gregorian.month,
        day: gregorian.day,
        dayOfWeek: gregorian.dayOfWeek,
        isYun: isYun
      }
      var icsEvent = {
        title: title,
        start: [gregorian.year, gregorian.month, gregorian.day],
        end: [gregorian.year, gregorian.month, gregorian.day]
      }
      this.events.push(event);
      this.icsEvents.push(icsEvent);
    }
  }
}
</script>

<style scoped>
.form-field {
  margin-bottom: 20px;
}

.form-field--with-icon {
  position: relative;
}

.form-field__icon {
  position: absolute;
  top: 50%;
  left: 14px;
  transform: translateY(-50%);
  width: 16px;
  height: 16px;
  pointer-events: none;
  z-index: 1;
}

.form-field__input {
  display: block;
  width: 100%;
  padding: 14px 16px;
  font-family: 'Noto Sans KR', sans-serif;
  font-size: 0.95rem;
  color: #e8e6e3;
  background: rgba(255, 255, 255, 0.05);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 10px;
  outline: none;
  transition: border-color 0.25s, box-shadow 0.25s;
}

.form-field__input--icon {
  padding-left: 40px;
}

.form-field__input::placeholder {
  color: #7b8cad;
}

.form-field__input:focus {
  border-color: #d4a574;
  box-shadow: 0 0 0 3px rgba(212, 165, 116, 0.15), 0 0 16px rgba(212, 165, 116, 0.1);
}

/* Date picker icon color invert for dark bg */
.form-field__input::-webkit-calendar-picker-indicator {
  filter: invert(0.8) sepia(0.2) hue-rotate(180deg);
  cursor: pointer;
}

.form-field__hint {
  font-size: 0.8rem;
  color: #7b8cad;
  margin-top: 6px;
  padding-left: 4px;
}

/* Buttons */
.actions {
  display: flex;
  gap: 10px;
  margin-top: 8px;
  margin-bottom: 32px;
}

.btn--primary,
.btn--ghost {
  flex: 1;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  padding: 10px 16px;
  font-family: 'Noto Sans KR', sans-serif;
  font-size: 0.9rem;
  font-weight: 500;
  border-radius: 10px;
  cursor: pointer;
  text-decoration: none;
  transition: all 0.25s;
}

.btn--primary {
  background: #d4a574;
  color: #0a0e27;
  border: 1px solid #d4a574;
}

.btn--primary:hover {
  background: #e0b88a;
  box-shadow: 0 0 16px rgba(212, 165, 116, 0.3);
}

.btn--primary.btn--disabled {
  opacity: 0.4;
  pointer-events: none;
}

.btn--ghost {
  background: transparent;
  color: #d4a574;
  border: 1px solid rgba(212, 165, 116, 0.4);
}

.btn--ghost:hover {
  background: rgba(212, 165, 116, 0.1);
  border-color: #d4a574;
}

/* Badges */
.badge--yun {
  display: inline-block;
  font-size: 0.7rem;
  font-weight: 500;
  color: #0a0e27;
  background: #d4a574;
  padding: 1px 8px;
  border-radius: 10px;
  vertical-align: middle;
}

.badge--dow {
  display: inline-block;
  font-size: 0.7rem;
  font-weight: 500;
  color: #e8e6e3;
  background: rgba(123, 140, 173, 0.3);
  padding: 1px 8px;
  border-radius: 10px;
  vertical-align: middle;
}

/* Event list */
.event-list {
  list-style: none;
  padding: 0;
  margin: 0;
  border-top: 1px solid rgba(255, 255, 255, 0.06);
}

.event-list__item {
  display: flex;
  align-items: center;
  padding: 10px 4px;
  font-size: 0.85rem;
  color: #c0bdb8;
  border-bottom: 1px solid rgba(255, 255, 255, 0.06);
  animation: itemSlideIn 0.35s ease-out both;
}

.event-list__badges {
  margin-left: auto;
  display: flex;
  gap: 6px;
}

@keyframes itemSlideIn {
  from {
    opacity: 0;
    transform: translateY(8px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
</style>
