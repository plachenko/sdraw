<script>
</script>

<div id="SDConnectContainer">
  <div class="row">
    <div class='item' style="background-color:#F00;">t</div>
    <div class='item' style="background-color:#FFF;">f</div>
    <div class='item' style="background-color:#F0F;">g</div>
  </div>
  <div class="row">
    <div class='item' style="background-color:#F00;">t</div>
    <div class='item' style="background-color:#FFF;">f</div>
    <div class='item' style="background-color:#F0F;">g</div>
  </div>
</div>

<style>
  .item{
    float:left;
  }
  .row{
    flex-direction: row;
    flex:1;
    }
  #SDConnectContainer{
    flex-direction: column;
    flex-wrap: wrap;
    display: flex;
    flex: 1;
    background-color:#F00;
    }

</style>
