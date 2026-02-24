<template>
  <div>
    <!-- event-form -->
    <div class="form-group">
      <input type="text" class="form-control" id="lunarEventName" aria-describedby="emailHelp"
        placeholder="음력일정명"
        v-model="lunarEventName"
        v-on:change="eventGenerate">
    </div>
    <div class="form-group">
      <input type="date" class="form-control" id="lunarEventDate" aria-describedby="solarEventDate"
        v-model="lunarEventDate"
        v-on:change="eventGenerate">
      <div id="solarEventDate" class="form-text text-muted">양력기준: {{ solarEventDate }} <span v-show="isYun">, {{ solarYunEventDate }} <span class="badge badge-warning" v-show="isYun">윤</span></span></div>
    </div>
    <div>
      <a class="btn btn-primary btn-sm btn-block"
          v-bind:class="{ disabled: events.length === 0 }"
          v-bind:href="icsContent"
          v-bind:download="filename">Calendar 등록</a>
      <button class="btn btn-danger btn-sm btn-block" v-on:click="resetEvent">리셋</button>
    </div>
    <!-- // event-form -->
    <!-- event-list -->
    <ul class="list-group list-group-flush">
      <li class="list-group-item"
        v-for="(event, index) in events"
        v-bind:key="index">
          양력: {{ event.date }} <span class="badge badge-secondary">{{ event.dayOfWeek }}요일</span> <span class="badge badge-warning" v-show="event.isYun">윤</span>
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

</style>
