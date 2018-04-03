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
      <div id="solarEventDate" class="form-text text-muted">양력기준: {{ solarEventDate }}</div>
    </div>
    <div>
      <a class="btn btn-primary btn-sm btn-block"
          v-bind:class="{ disabled: events.length === 0 }"
          v-bind:href="icsConent"
          download="test.ics">Calendar 등록</a>
      <button class="btn btn-danger btn-sm btn-block" v-on:click="resetEvent">리셋</button>    
    </div>
    <!-- // event-form -->
    <!-- event-list -->
    <ul class="list-group list-group-flush">
      <li class="list-group-item"
        v-for="(event, index) in events" 
        v-bind:key="index">
          양력: {{ event.date }} <span class="badge badge-secondary">{{ event.dayOfWeek }}요일</span>
      </li>
    </ul>
    <!-- // event-list -->
  </div>
</template>

<script>
var holidayKR = require('holiday-kr');
var ics = require('ics');

new Date().toISOString().split('T')[0];
var today = holidayKR.getLunar(new Date());

export default {
  components: {
  },
  data: function() {
    return {
      'lunarEventName'  : '',
      'lunarEventDate'  : [today.year, this.padder(today.month), this.padder(today.day)].join('-'),
      'events'          : [],
      'icsEvents'       : []
    }
  },
  computed: {
    icsConent: function() {
      const { error, value } = ics.createEvents(this.icsEvents)
      if (error) { return '' }
      return 'data:text/calendar;charset=utf-8,' + encodeURIComponent(value)
    },
    solarEventDate: function() {
      let [year, month, day] = this.lunarEventDate.split('-');
      if (year < 1900) { return '' }
      var gregorian = holidayKR.getSolar(year, month, day)
      return [gregorian.year, this.padder(gregorian.month), this.padder(gregorian.day)].join('-')
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
          var gregorian = holidayKR.getSolar(i, month, day);
        } 
        catch(err) {
          return;
        }
        var event = {
          date: [gregorian.year, this.padder(gregorian.month), this.padder(gregorian.day)].join('-'),
          year: gregorian.year,
          month: gregorian.month,
          day: gregorian.day,
          dayOfWeek: gregorian.dayOfWeek
        }
        var icsEvent = {
          title: this.lunarEventName + ' (음력: ' + [month, day].join('-') + ')',
          start: [gregorian.year, gregorian.month, gregorian.day],
          end: [gregorian.year, gregorian.month, gregorian.day]
        }
        this.events.push(event);
        this.icsEvents.push(icsEvent);
      }
    },
    resetEvent: function() {
      this.events = [];
      this.icsEvents = [];
      this.lunarEventDate = [today.year, this.padder(today.month), this.padder(today.day)].join('-'),
      this.lunarEventName = '';
    },
    padder: function(integer) {
      var str = "" + integer;
      var pad = "00";
      return (pad + str).slice(-pad.length);
    }
  }
}
</script>

<style scoped>

</style>