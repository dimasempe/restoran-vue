
<template lang="">
  <NavBar :user-name = 'userName' :role-id = 'roleId'/>
  <div>
    {{userName}}
    {{roleId}}
  </div>
</template>
<script>
import router from '@/router';
import NavBar from '@/components/NavBar.vue'

export default {
  data() {
    return {
      userName:'',
      roleId: ''
    }
  },
  mounted() {
    this.userName = localStorage.getItem('name')
    this.roleId = localStorage.getItem('role_id')
    if(!this.userName || this.userName == null){
      router.push({name: 'login'})
    }
  },
  components: {
    NavBar
  },
}
</script>
<style lang="">
  
</style>