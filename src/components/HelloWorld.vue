<template>
  <div class="table">
    <div class="backDrop"
         :class="{ covered: deskShown }"
         @click="deskShown =! deskShown"
        >
    </div>
      <table class="table-wrap">
        <thead>
        <tr class="table-wrap__header">
          <th @click="sort('second_name')">ФИО</th>
          <th @click="sort('equipment_amount')">Кол-во</th>
          <th @click="sort('overall_cost')">Общая стоимость</th>
        </tr>
        </thead>
        <tbody>
        <tr
                class="table-wrap__list"
                v-for="data in sortedList"
                :class="{ active: data.id == choosen }"
                @click="choosen=choosen===data.id? null : data.id "
                @contextmenu="DeleteHandler($event , data.id)"
                @dblclick="FullInfoShowHandler(data)"
        >
          <td>{{FioFieldHandler(data)}}</td>
          <td>{{data.equipment_amount}}</td>
          <td>{{data.overall_cost}}</td>
        </tr>
        </tbody>
      </table>
      <p>
        <button @click="prevPage">Previous</button>
        <button @click="nextPage">Next</button>
      </p> page={{currentPage}}

  <transition name="slide">>
      <div
              v-if="deskShown"
              class="desk">
        <div class="desk-owner">
          <div class="desk-owner__fio">
            <div>Имя:</div>
            <input :placeholder= desk.first_name />
          </div>
          <div class="desk-owner__fio">
            <div>Фамилия:</div>
            <input :placeholder= desk.second_name />
          </div>
            <div class="desk-owner__fio">
            <div>Отчество:  </div>
            <input :placeholder= desk.patronymic />
          </div>
        </div>
        <table class="table-wrap to-left">
          <thead>
          <tr class="table-wrap__header">
            <th>№</th>
            <th>Название</th>
            <th>Стоимость</th>
          </tr>
          </thead>
          <tbody>
          <tr
                  class="table-wrap__list"
                  v-for="(data, index) in desk.equipment"

          >
            <td>{{ index+1}}</td>
            <td><input :placeholder= data[0] /></td>
            <td><input :placeholder= data[1] /></td>
          </tr>
          <tr>
            <td></td>
            <td>Итого</td>
            <td>{{desk.overall_cost}}</td>
          </tr>
          </tbody>
        </table>
        <div class="desk-manipulate">
          <button v-if="" class="desk-manipulate__edit">Edit</button>
          <button class="desk-manipulate__save">Save</button>
          <button @click="deskShown=false" class="desk-manipulate__cancel">Cancel</button>
        </div>
      </div>
  </transition>
  </div>
</template>

<script>
import JsonData from '../list.json'

export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  data() {
    return {
      list: JsonData,
      currentSort:'name',
      currentSortDir:'asc',
      pageSize: 3,
      currentPage :1,
      choosen: null,
      desk: null,
      deskShown: false
    }
  },
  methods:{
    asd(asd) {
      alert (asd);
    },
    sort:function(s) {
      if(s === this.currentSort) {
        this.currentSortDir = this.currentSortDir==='asc'?'desc':'asc';
      }
      this.currentSort = s;
    },
    nextPage:function() {
      if((this.currentPage*this.pageSize) < this.list.length) this.currentPage++;
    },
    prevPage:function() {
      if(this.currentPage > 1) this.currentPage--;
    },
    FioFieldHandler(value) {
      return value.second_name + ' '  + value.first_name.slice(0,1) + '.'  + value.patronymic.slice(0,1)
    },
    DeleteHandler(e, id) {
      e.preventDefault();
      this.choosen = id;
    },
    FullInfoShowHandler(data) {
      this.deskShown = true;
      this.desk = data;
    }
  },
  computed:{
    sortedList:function() {
      return this.list.sort((a,b) => {
        let modifier = 1;
        if(this.currentSortDir === 'desc') modifier = -1;
        if(a[this.currentSort] < b[this.currentSort]) return -1 * modifier;
        if(a[this.currentSort] > b[this.currentSort]) return 1 * modifier;
        return 0;
      }).filter((row, index) => {
        let start = (this.currentPage-1)*this.pageSize;
        let end = this.currentPage*this.pageSize;
        if(index >= start && index < end) return true;
      });
    }
  }

}
</script>

<style scoped>
  .table-wrap {
    text-align: center;
    margin: 0 auto;
    max-width: 900px;
    width: 100%;
  }
  .table-wrap__header {
    cursor: pointer;
    background-color: #616161;
    height: 50px;
  }
  .table-wrap__list {
    background-color: #fff;
    color: #000;
  }
  .active {
    background-color: #5488af;
  }
  th {
    border: 1px solid gray;
    border-bottom: none;
    border-top: none;
    color: white;
  }
  .covered {
    width: 100%;
    height: 100%;
    position: fixed;
    z-index: 99;
    left: 0;
    top: 0;
    background-color: rgba(0,0,0,.8);
  }
  .desk {
    position: absolute;
    left: 0;
    right: 0;
    top: 10%;
    margin-left: auto;
    margin-right: auto;
    width: 800px;
    background-color: white;
    z-index: 999;
    padding: 25px;
  }
  .desk-owner {
    text-align: left;
    display: flex;
    justify-content: space-between;
    padding-bottom: 25px;

  }
  input{
    padding: 12px 5px;
    border: 1px solid #ccc;
    border-radius: 4px;
    resize: vertical;
    width: 100%;
  }
  input:focus{
    background-color: lightblue;
  }
  .to-left input{
    text-align: right;
  }
  .to-left {
    text-align: right;
  }
  .desk-manipulate button {
    border: none;
    color: white;
    padding: 15px 32px;
    text-align: center;
    text-decoration: none;
    display: inline-block;
    font-size: 16px;
    margin: 4px 2px;
    cursor: pointer;
  }
  .desk-manipulate__edit {
    background-color: #008CBA;
  }
  .desk-manipulate__save {
    background-color: #4CAF50;
  }
  .desk-manipulate__cancel {
    background-color: #f44336;
  }
  .slide-enter-active {
    animation: slide-in 0.5s ease-out forwards;
  }
  .slide-leave-active {
    animation: slide-out 0.5s ease-out forwards;
  }
  @keyframes slide-in {
    from {
      transform: translateY(-100%);
    }
    to {
      transform: translateY(0);
    }
  }
  @keyframes slide-out {
    from {
      transform: translateY(0);
    }
    to {
      transform: translateY(-100%);
    }
  }
</style>
