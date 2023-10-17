<script>

    import Calendar from './Calendar.svelte';
    import Metric from './metric.svelte';
    import Time from './time.svelte'
    import Placement from './placement.svelte';
    import Reject from './rejectedreasons.svelte';


  </script>
  <div class="main-container">
  <Calendar/>
  <Metric/>
  <Time/>
  <Placement/>
  <Reject/>
 </div>
<style>
    .main-container {
  
        width: 1520px;
        height: 2000px;
        margin-left:0px;
        margin-top:-165px;
    }

 
  
</style>