<template>
  <div>
    <nav>
      <label for="deltaNumber">Number of items:
        <input type="number" id="deltaNumber" name="deltaNumber"
               v-model="deltaNumber"
               min="0" :max="dataLength"
               @change=changeNumber($event)>
      </label>
      <label for="start">Start date:
        <input type="date" id="end" name="trip-end"
               v-model="lastShowedDate"
               :min=lastDate :max=firstShowedData
               @change=changeLastDate($event)>
      </label>
      <label for="end">End date:
        <input type="date" id="start" name="trip-start"
               v-model=firstShowedData
               :min=lastShowedDate :max=firstData
               @change=changeFirstDate($event)>
      </label>
    </nav>
    <table>
      <thead>
      <tr>
        <th></th>
        <th>ricoverati con sintomi - hospitalized with symptoms</th>
        <th>terapia intensiva - intensive care</th>
        <th>totale ospedalizzati- total hospitalized</th>
        <th>variazione totale positivi - total positive change</th>
        <th>nuovi positivi - new positives</th>
        <th>deceduti - deceased</th>
        <th>totale casi - total cases</th>
        <th>ingressi terapia intensiva - intensive care entrances</th>
      </tr>
      </thead>
      <tbody>
      <tr v-for="(d) in data" :key="d.data">
        <th>{{new Date(d.data).toLocaleDateString()}}</th>
        <td>{{d.ricoverati_con_sintomi}}</td>
        <td>{{d.terapia_intensiva}}</td>
        <td>{{d.totale_ospedalizzati}}</td>
        <td>{{d.variazione_totale_positivi}}</td>
        <td>{{d.nuovi_positivi}}</td>
        <td>{{d.deceduti}}</td>
        <td>{{d.totale_casi}}</td>
        <td>{{d.ingressi_terapia_intensiva}}</td>
      </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import getData from './getData'
export default {
  name: 'tableApp',
  props: {
  },
  data() {
    return {
      json : [],
      data : [],
      dataLength : 0,
      number : 0,
      deltaNumber : 0,
      firstItem : 0,
      lastItem : 0,
      firstData : "",
      lastDate : "",
      lastShowedDate : "",
      firstShowedData : ""
    }
  },
  async mounted() {
    const res = await fetch("https://raw.githubusercontent.com/pcm-dpc/COVID-19/master/dati-json/dpc-covid19-ita-andamento-nazionale.json");
    const dataRaw = await res.json();
    this.json = dataRaw.reverse()
    this.number = 10;
    this.firstItem = 0;
    this.firstData = getData(this.json[this.firstItem].data);
    this.firstShowedData = this.firstData;
    this.lastItem = this.number-1;
    this.lastDate = getData(this.json[this.json.length-1].data);
    this.lastShowedDate = getData(this.json[this.number-1].data);
    this.dataLength = this.json.length;
    this.deltaNumber = this.number;
    this.data = this.json.slice(this.firstItem,this.number);
    document.querySelector(".loading").classList.remove("loading");
  },
  methods: {
    changeNumber(e) {
      let delta = this.firstItem+(+e.target.value);
      let lastItemNumber = delta-1;//delta >= this.state.data.length ? this.state.data.length-1 : delta;
      this.lastShowedDate = getData(this.json[lastItemNumber].data);
      this.number = delta;
      this.deltaNumber = +e.target.value;
      this.lastItem = lastItemNumber;
      this.data = this.json.slice(this.firstItem,this.number);
    },
    changeFirstDate(e){
      let valueDate = e.target.value
      let lastItem = this.lastItem;
      let lastItemNumber = lastItem >= this.json.length ? this.json.length-1 : lastItem;
      let lastItemDate = this.json[lastItemNumber].data.split("T")[0];
      let d1 = new Date(valueDate);
      let d2 = new Date(lastItemDate);
      const diffTime = Math.abs(d2 - d1);
      const diffDays = Math.ceil(diffTime / (1000 * 60 * 60 * 24));
      this.firstItem= lastItemNumber - (diffDays);
      this.firstShowedData = valueDate;
      this.deltaNumber = diffDays+1;
      this.data = this.json.slice(this.firstItem,this.number);
    },
    changeLastDate(e){
      window.performanceMeasurement = performance.now();
      let d1 = new Date(this.json[this.firstItem].data.split("T")[0]);
      let d2 = new Date(e.target.value);
      const diffTime = Math.abs(d2 - d1);
      const diffDays = Math.ceil(diffTime / (1000 * 60 * 60 * 24));
      let number = this.firstItem + diffDays;
      let lastItemNumber = number; //number >= this.state.data.length ? this.state.data.length-1 : number;
      this.lastShowedDate = getData(this.json[lastItemNumber].data);
      this.lastItem = lastItemNumber;
      this.number = number+1;
      this.deltaNumber = diffDays+1;
      this.lastItem = lastItemNumber;
      this.data = this.json.slice(this.firstItem,this.number);
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
nav{position:sticky;top:0;padding:8px;background:#fff}
table{border:1px solid;border-collapse: collapse;text-align:center}
thead{position:sticky;top:40px;background:#fff;}
tr,td,thead th{border:1px solid;}
td,tr th{padding:0.5em}
</style>
