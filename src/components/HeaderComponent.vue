<template>
    <header></header>

</template>

<script>
export default {
    name: 'HeaderComponent'
}
</script>

<style lang="scss" scoped>
header {
    background-image: url('/public/header.jpg');
    // background-position: center;
    height: 200px;
    margin-bottom: 20px;
}
@media (max-width: 992px) {
   header {
       background-image: url('/public/header-sm.jpg');
       background-repeat: no-repeat;
       background-size: cover;
       
   }
}
</style>