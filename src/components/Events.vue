<template>
  <div class="events">
    <div class="events__buttons">
      <button type="button" name="button" @click="getSchedules('yesterday')" v-bind:class="{ active: selectedDay.yesterday }">Hier</button>
      <button type="button" name="button" @click="getSchedules('today')" v-bind:class="{ active: selectedDay.today }">Aujourd'hui</button>
      <button type="button" name="button" @click="getSchedules('tomorow')" v-bind:class="{ active: selectedDay.tomorow }">Demain</button>
    </div>
    <div class="card" v-for="event in schedules" :key="event.id">
      <div class="card__title">
        <strong>{{ $moment(event.start).locale('fr').format('H:mm') }}</strong> à <strong>{{ $moment(event.end).locale('fr').format('H:mm') }}</strong>
      </div>
      <div class="card__content">
        {{ event.name }} <br/>
        <small v-if="event.location">au {{ event.location }} (sauf cours et TD à l'espace habituel)</small>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Events',
  data() {
    return {
      selectedDay: {
        yesterday: false,
        today: true,
        tomorow: false,
      },
      schedules: []
    }
  },
  mounted() {
    this.getSchedules('today');
  },
  methods: {
    async getSchedules(dateSelected) {
      this.selectedDay.yesterday = false
      this.selectedDay.today = false
      this.selectedDay.tomorow = false
      try {
        let timeMax = this.$moment().endOf('day').toISOString()
        let timeMin = this.$moment().startOf('day').toISOString()
        if (dateSelected === 'tomorow') {
          this.selectedDay.tomorow = true
          timeMax = this.$moment().add(1, 'days').endOf('day').toISOString()
          timeMin = this.$moment().add(1, 'days').startOf('day').toISOString()
        } else if (dateSelected === 'yesterday') {
          this.selectedDay.yesterday = true
          timeMax = this.$moment().subtract(1, 'days').endOf('day').toISOString()
          timeMin = this.$moment().subtract(1, 'days').startOf('day').toISOString()
        } else {
          this.selectedDay.today = true
        }
        const params = {
          apiKey: 'AIzaSyC7FQx8-Ut7-I1EWyvmlBBrVwXXO6nHOEo',
          calendarId: 'a0vmbl97guovvv12aub1b6b3vk@group.calendar.google.com',
          timeMax: timeMax,
          timeMin: timeMin
        }
        let [schedulesRes] = await Promise.all([
          this.$axios.get(`https://www.googleapis.com/calendar/v3/calendars/${params.calendarId}/events?timeMax=${params.timeMax}&timeMin=${params.timeMin}&key=${params.apiKey}`)
        ])

        // Clean data
        let dayEvents = [];
        for (var i = 0; i < schedulesRes.data.items.length; i++) {
          const event = {
            id: schedulesRes.data.items[i].id,
            status: schedulesRes.data.items[i].status,
            name: schedulesRes.data.items[i].summary,
            location: schedulesRes.data.items[i].location,
            start: schedulesRes.data.items[i].start.dateTime,
            end: schedulesRes.data.items[i].end.dateTime
          }
          if (event.status === 'confirmed') {
            dayEvents.push(event);
          }
        }
        dayEvents.sort((a, b) => this.$moment(a.start).format('Hmmss') - this.$moment(b.start).format('Hmmss'))

        this.schedules = dayEvents
      } catch (e) {
        console.log('error: no result', e)
      }
    }
  }
}
</script>

<style>
.events__buttons {
  margin-bottom: 20px;
}
button {
  color: white;
  padding: 10px;
  margin: 5px;
  background: #ee0979;
  overflow: hidden;
  border-radius: 6px;
  font-family: 'Open Sans', sans-serif;
  border: none;
  box-shadow: 2px 2px 4px rgba(0, 0, 0, 0.5);
  margin-bottom: 15px;
  box-shadow: 2px 2px 4px rgba(0, 0, 0, 0.5);
  cursor: pointer;
}
button.active {
  background: #ff6a00;
}
.card {
  color: white;
  padding: 14px 26px;
  font-size: 0.8rem;
  background: #ee0979;
  background: -webkit-linear-gradient(to right, #ff6a00, #ee0979);
  background: linear-gradient(to right, #ff6a00, #ee0979);
  overflow: hidden;
  border-radius: 6px;
  font-family: 'Open Sans', sans-serif;
  border: none;
  box-shadow: 2px 2px 4px rgba(0, 0, 0, 0.5);
  margin-bottom: 15px;
}
.card__title {
  font-size: 20px;
  margin-bottom: 10px;
  font-weight: bold;
  text-transform: uppercase;
  letter-spacing: 1.5px;
}
.card__content {
  font-size: 14px;
  font-weight: 600;
}
</style>
