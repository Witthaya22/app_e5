<script>
import axios from 'axios'

export default{

    data(){
        return{
            students:[
                {id:'1', username:'Witthaya1', password:'123'},
                {id:'2', username:'Witthaya2', password:'123'},
                {id:'3', username:'Witthaya3', password:'123'},
                {id:'4', username:'Witthaya4', password:'123'},
            ]
        }
    },
    async created(){
    const res = await axios.get('http://localhost:7000/list')
    const data = await res.data
    this.students = data.row
    console.log(data)
   }
}
</script>

<template>
    <div>
        <h1>แสดงข้อมูลนักศึกษา</h1>   
        <ul>
            <li v-for="student in students">
                <span>{{ student.id }}</span>
                <span>{{ student.username }}</span>
                <span>{{ student.password }}</span>
            </li>
        </ul>
    </div>
</template>