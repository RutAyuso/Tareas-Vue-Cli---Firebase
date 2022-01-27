<template>
<center>
 <div id="appVue">
        <h1 id="titulo">YOUR LIST</h1>
        Buscar tarea:
        <input type="text" v-model="buscar"><hr>
        Nueva:
        <input type="text" v-model="titulo" @keyup.enter="añade">
        <button id="añade" v-on:click="añade" >ADD</button><!--AÑADIR KEYPRESS A BOTONES-->
        <br>
        <br>
        <div id="contador">Total tareas: {{tareas.length}}  
                           Tareas acabadas: {{cuenta}}</div>

            <transition-group name="fade">
            <div id="cuadradito" v-for="t,i in miListaFiltrada" v-bind:key="i">

                
                    <div id="cuadrado">
                        <hr>
                        <section v-bind:class="{verdecito: t.completada, rojito: !t.completada }">
                            <p>Descripción: {{t.titulo}}</p>
                            <!-- <p>Completa : {{t.completada}}</p> -->
                            <p> Fecha: {{t.fecha}}.</p>
                            <p>Prioridad: {{t.prioridad}}</p>
                        </section>
                      
                        <!-- RADIO BOTONES PRIORIDAD -->
                        <label for="0">Irrelevante</label><input name="prioridad" value="0" v-model="t.prioridad" type="radio">
                        <label for="1">Importante</label><input name="prioridad" value="1" v-model="t.prioridad" type="radio"><br>
                        <label for="2">Muy importante</label><input name="prioridad" value="2" v-model="t.prioridad" type="radio"><br><br>
    
                        <!-- BTN DE COMPLETAR Y BORRAR -->
                        <button id="completar" v-on:click="completar(i)">✅</button>
                        <button id="borrar" v-on:click="borrar(i)">&#10060;</button>
                    
                    </div>                      
            
            </div>
           
         </transition-group>
        <hr>
        <div id="botones">
          <button id="borrar_completadas" v-on:click="borrarTareasCompletadas"> DELETE ALL COMPLETED </button><br>
          <button id="mostrar_completadas" v-on:click="mostrarTareasCompletadas">COMPLETED</button>
          <button id="mostrar_activas" v-on:click="mostrarTareasActivas">ACTIVE</button>
          <button id="mostrar_todas" v-on:click="mostrarTodasTareas">ALL</button>
        </div>
        
        
</div>
</center>
</template>

<script>


export default {
  name: 'App',
  components: {
    
  },
  data() {
      return {
        tareas:[],
        titulo:"",

        completas:false,
        incom:false,
        todas:false,
        completadas:[],
        incompletas:[],
        buscar:''
    }
    }
    ,
    methods:{
        añade(){
          this.tareas.push({titulo:this.titulo, completada: false, fecha: new Date().toLocaleString(), prioridad: 0});
          this.anadirLocalStorage(); 
        },
        completar(i){   
            this.tareas[i].completada = !this.tareas[i].completada;
            this.anadirLocalStorage();//llamamos al metodo
          },
        borrar(i){
          this.tareas.splice(i,1);
          this.anadirLocalStorage();//llamamos al metodo 
        },
        borrarTareasCompletadas(){
          this.tareas=this.tareas.filter((nota)=>{
              return !nota.completada;
          })
          this.anadirLocalStorage();//llamamos al metodo 
        },
        anadirLocalStorage(){
          localStorage.setItem('notas', JSON.stringify(this.tareas));
        },

        
        mostrarTareasCompletadas(){
         this.completas=!this.completas;
         this.incom= false;
         this.todas=false;
        },
        mostrarTareasActivas(){
          this.incom= !this.incom;
          this.completas=false;
          this.todas=false;
        },
        mostrarTodasTareas(){
          this.todas=!this.todas;
          this.incom= false;
          this.completas=false;
        },


      anadirCompletadas(){//AÑADO AL ARRAY DE COMPLETADAS TODAS LAS QUE TENGAN 'COMPLETADA:TRUE'
        console.log('aa')
        if(this.tareas.length >= 1){
          for(let t of this.tareas){
            if(t.competada == true){
              this.completadas.push(t);
             }
          }
          return this.completadas;
        }
        return 0;
      },
      anadirIncompletas(){
        if(this.tareas.length >= 1){//AÑADO AL ARRAY DE INCOMPLETAS TODAS LAS QUE TENGAN 'COMPLETADA:FALSE'
          for(let t of this.tareas){
            if(t.competada == false){
              this.incompletas.push(t);
             }
          }
          return this.incompletas;
        }
        return 0
        
      }
        
    },
    computed:{

      miListaFiltrada: function(){
        let listaFiltrada;
        if(this.buscar==""){
            listaFiltrada=this.tareas;
        }
        else{
            listaFiltrada= this.tareas.filter((nota)=>{
                if(nota.titulo.indexOf(this.buscar)>=0){
                    return true;
                }else{
                    return false;
                }
            }); 
        
            
      }
      listaFiltrada=listaFiltrada.sort((nota1, nota2)=>{//SORT, ORDEN DE MAYOR A MENOR PRIORIDAD
        if(nota1.prioridad>nota2.prioridad){
            return -1;
        }
        if(nota1.prioridad<nota2.prioridad){
            return 1;
        }
        return 0;
    })
        if(this.todas==true){
          listaFiltrada= this.tareas.filter((nota)=>{
            if(nota.completada==true || nota.completada==false){//TODAS
                return true;
            }else{
                return false;
            }
        }); 
      }
        if(this.completas==true){
          listaFiltrada= this.tareas.filter((nota)=>{//FILTRO COMPLETADAS
            if(nota.completada==true){
                return true;
            }else{
                return false;
            }
        });
      }
        if(this.incom==true){
          listaFiltrada= this.tareas.filter((nota)=>{//FILTRO INCOMPLETAS
            if(nota.completada==false){
                return true;
            }else{
                return false;
            }
        });
        }

        return listaFiltrada;
    },
      cuenta(){
        var terminadas = 0;
        for(let i = 0; i<this.tareas.length; i++){//CONTADOR COMPLETADAS
          if(this.tareas[i].completada){
              terminadas++;
          }
        }
        return terminadas;
      },



      },
    mounted(){//ONLOAD
      if(localStorage.getItem('notas')){
        this.tareas = JSON.parse(localStorage.getItem('notas'));

        // $('uno').show();
        /*$('dos').hide();
        $('tres').hide();*/
      }
    }
}
</script>

<style>
/****ESTILOS BODY****/
html {
    height: 100%;
    /* overflow: hidden; */
  }
body{
    /*background-color: rgb(75, 143, 155);*/
    margin-top: 5%;
    /* zoom: 150%; */
    background: conic-gradient(#bebc79, #2d6459, rgb(124, 49, 61), rgb(220, 176, 176), rgb(163, 208, 163));
    background-attachment: fixed;
    font-family: Verdana, Geneva, Tahoma, sans-serif;
    font-size: small;
    display: flex;
    justify-content: space-around;
    /*background: rgb(176,174,238);
    background: radial-gradient(circle, rgba(176,174,238,1) 15%, rgb(85, 18, 75) 90%);*/
}

/****ESTILOS CONTENEDOR VUE PRINCIPAL****/
#appVue{
    color: rgb(0, 0, 0);
    background-color: rgb(222, 235, 231);
    max-width: 350px;
    border-radius: 20px;
    padding-top: 10px;
    padding-bottom: 10px;
    align-content: center;
    
}
/****TITULO****/
#titulo{
    -webkit-text-stroke: 1px rgb(0, 0, 0);
    color: white;
}

/****ESTILOS CONTENEDOR DE TODAS TAREAS Y BOTONES****/
#cuadradito{
    position: inherit;
    align-self: center;
    border-radius: 10px;
    margin: 0%;
    padding-top: 10px;
    padding: 5%;
    align-content:space-between; 
    overflow: auto;
    
}

/****ESTILOS CONTENEDOR TAREA****/
/*
#cuadrado{
    border-radius: 50px;
}*/

#botones{
  position: sticky;

}
/****ESTILOS CUANDO TAREA COMPLETA****/
.verdecito{
    margin: 1 auto;
    padding:0;
    color:rgb(6, 63, 6);
    background-color: rgb(193, 236, 150);
    text-decoration:line-through;
}

/****ESTILOS CUANDO TAREA NO COMPLETA****/
.rojito{
    margin: 1 auto;
    padding:0;
    color:rgb(185, 38, 28);
    background-color: rgb(240, 161, 161);
}

/****ESTILOS BTN 'AÑADIR' TAREA****/
#añade{
    background-color: rgb(0, 0, 0);
    color: rgb(255, 255, 255);
    font-family: Arial, Helvetica, sans-serif;
    font-weight: bold;
    border-color: black;
    align-self: center;
}

/****ESTILOS BTN 'DONE' ****/
#completar{
    background-color: rgb(166, 230, 166);
    font-weight: bold;
    border-radius: 10px;
    align-self: center;
}

/****ESTILOS BTN 'BORRAR' ****/
#borrar{
    background-color: rgb(235, 179, 179);
    font-weight: bold;
    border-radius: 10px;
    align-items: center;
}

/****ESTILOS BTN 'DELETE ALL DONE' ****/
#borrar_completadas{
    background-color: rgba(157, 146, 157, 0.974);
    color: rgb(0, 0, 0);
    border-radius: 10px;
    border-color: black;
    border-width: 2px;
}

/****ESTILOS BTN 'COMPLETED' ****/
#mostrar_completadas{
    background-color: rgb(191, 240, 211);
    color: rgb(0, 0, 0);
    border-radius: 10px;
    border-color: black;
    border-width: 2px;
}

/****ESTILOS BTN 'ACTIVE' ****/
#mostrar_activas{
    background-color: rgb(202, 199, 149);
    color: rgb(0, 0, 0);
    border-radius: 10px;
    border-color: black;
    border-width: 2px;
}

/****ESTILOS BTN 'ALL' ****/
#mostrar_todas{
    background-color: rgb(155, 170, 194);
    color: rgb(0, 0, 0);
    border-radius: 10px;
    border-color: black;
    border-width: 2px;
}
/****ESTILOS CONTADOR SUPERIOR****/
#contador{
    background-color: rgb(255, 255, 255);
    align-items: center;
    margin-right: 15px;
    margin-left: 15px;
    border-radius: 20px;
}
/****TRANSICIONES DE BORRADO****/
/*En esta transicion manda al ultimo lugar la tarea completada y la elimina poco a poco*/
.fade-leave-active {
    transition: all 1s cubic-bezier(1, 0.5, 0.8, 1);
  }
  
  .fade-enter-from,
  .fade-leave-to {
    transform: translateX(20px);
    opacity: 0;
  }

</style>
