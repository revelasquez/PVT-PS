<template>
 <v-card class="elevation-12">
    <v-card-title>
      Prestamos en Mora Parcial
      <v-btn small @click="download" 
      :disabled="dialog"
      :loading="dialog"
      icon  
        > <v-icon color="success"> fa-file-excel-o</v-icon>
        </v-btn>
      
        <v-dialog
          v-model="dialog"
          hide-overlay
          persistent
          width="300"
        >
          <v-card
            color="primary"
            dark
          >
            <v-card-text>
              Por favor espere
              <v-progress-linear
                indeterminate
                color="white"
                class="mb-0"
              ></v-progress-linear>
            </v-card-text>
          </v-card>
        </v-dialog>
      <v-spacer></v-spacer>
      
      <v-text-field
        v-model="search"
        append-icon="search"
        label="Buscar"
        single-line
        hide-details
      ></v-text-field>
       
    </v-card-title>
    <v-data-table
      :headers="headers"
      :items="loans"
      :search="search"
    >
      <template slot="items" slot-scope="props">
        <td class="text-xs-left">{{ props.item.PresNumero }}</td>
        <td class="text-xs-left">{{ props.item.PresSaldoAct }}</td>
        <td class="text-xs-left">{{ props.item.PadMatricula }}</td>
        <td class="text-xs-left">{{ props.item.PadCedulaIdentidad }}</td>
        <td class="text-xs-left">{{ props.item.PadNombres }}</td>
        <td class="text-xs-left">{{ props.item.PadNombres2do }}</td>
        <td class="text-xs-left">{{ props.item.PadPaterno }}</td>
        <td class="text-xs-left">{{ props.item.PadMaterno }}</td>
        <td class="text-xs-left">{{ props.item.PadTipo }}</td>
        
        <td class="text-xs-left"> <a  v-bind:href="generate_link(props.item.IdPrestamo)"><v-icon>assignment</v-icon></a> </td>
      </template>
      <v-alert slot="no-results" :value="true" color="error" icon="warning">
        Su busqueda para "{{ search }}" no se encontraron resultados.
      </v-alert>
    </v-data-table>
  </v-card>
</template>
<script>
export default {
    data() {
            return {
            loans: [],
            search: '',
            headers: [
              { text: 'Numero de Prestamo', value: 'PresNumero' },
              { text: 'Saldo', value: 'PresSaldoAct' },
              { text: 'Matricula', value: 'PadMatricula' },
              { text: 'CI ', value: 'PadCedulaIdentidad' },
              { text: '1er Nombre', value: 'PadNombres' },
              { text: '2do Nombre ', value: 'PadNombres2do' },
              { text: 'Paterno ', value: 'PadPaterno' },
              { text: 'Materno ', value: 'PadMaterno' },
              { text: 'Tipo ', value: 'PadTipo' },
              { text: 'Accion ' }
            ],
            dialog: false,
            }
    },
    created(){
          axios.get('/api/partial_loans')
           .then((response)=>{
                console.log('obteniendo lista ')
                this.loans = response.data;
            });

            
            console.log(moment().format('L'));
            console.log(moment().locale());
  
    },
    
    // define methods under the `methods` object
    methods: {
      generate_link(id){
          return 'http://sismu.muserpol.gob.bo/musepol/akardex.aspx?'+id;
        //console.log(this.loans)
      },
       download: function (event) {
        // `this` inside methods point to the Vue instance
        self = this;
        self.dialog = true
      
        axios({
            url: '/api/partial_loans',
            method: 'GET',
            params: {excel:true},
            responseType: 'blob', // important
          }).then((response) => {
            const url = window.URL.createObjectURL(new Blob([response.data]));
            const link = document.createElement('a');
            link.href = url;
            link.setAttribute('download', 'mora parcial '+moment().format()+'.xls');
            document.body.appendChild(link);
            link.click();
              self.dialog = false;
          });
      }
    
    },
    watch: {
      dialog (val) {
        if (!val) return

        //setTimeout(() => (this.dialog = false), 4000)
      }
    }
}
</script>

