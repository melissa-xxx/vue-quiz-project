<template>
  <!-- results section -->
  <div id="results" class="center">
    <h2>Results</h2>
    <span :amount="amount" :total="total"
      >You got {{ amount }} / {{ total }} questions correct</span
    >
    <p :grade="grade" class="red-txt">{{ grade }} %</p>
  </div>
</template>

<script>
export default {
  props: ["grade", "amount", "total"],
};
</script>

<style>
#results h2 {
  font-weight: 500;
  text-decoration-line: underline;
}
#results p {
  font-weight: 700;
  font-size: 25px;
}
</style>
