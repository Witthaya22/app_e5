<template>
    <h1>แสดงข้อมูลนักศึกษา{{ username }}</h1>
</template>
<script>
 import axios from 'axios'
 export default{
    data: () => ({
        id : '',
        username: '',
        password: '',
        status: '',
        email: '',
        picture: ''
    }),
   async created(){
    const res = await axios.get('http://localhost:7000/list')
    const data = await res.data
    console.log(data)
   }
 }
</script>