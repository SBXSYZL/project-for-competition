<template>
  <div style="margin-top: 1%;min-width: 1000px;min-height: 100%">
    <div style="margin-top: 60px;display: flex;width:70%;padding-right: 15%;padding-left: 15%;">
      <!--左边图片 start-->
      <div class="left_div">
        <div class="block">
          <el-carousel>
            <el-carousel-item v-for="item in 4" :key="item">
              <img style="height: 100%;width: 100%" src="http://pdd.91miaoshou.com/app/views/static/img/pdd/banner.jpg"
                   alt="">
            </el-carousel-item>
          </el-carousel>
        </div>

      </div>
      <!--左边图片 end-->
      <!--右边购买 start-->
      <div class="right_div">
        aaaaaaaaaaaaaaa
      </div>
      <!--右边购买 end-->
    </div>
  </div>
</template>

<script>
  export default {
    name: 'ProductInfo'
  }
</script>

<style scoped>
  .left_div {
    width: 50%;
    height: 50%;
  }

  .right_div {
    width: 50%;
    height: 50%;
  }
</style>
