<template>
  <div>
    <br>
    <div class="card">
      <div class="card-body">
        <h5 class="card-title">음력일정 간단등록</h5>
        <p class="card-text">2050년까지만 지원합니다.</p>
      </div>
    </div>
    <br>
    <event-form></event-form>
  </div>
</template>

<script>
import EventForm from './components/EventForm.vue'

export default {
  components: {
    'event-form': EventForm
  },
  data: function() {
    return {
    }
  }
}
</script>
