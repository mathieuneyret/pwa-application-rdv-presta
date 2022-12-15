<script setup lang="ts">
import "leaflet/dist/leaflet.css"
import "leaflet.locatecontrol"
import L from 'leaflet'
import {onMounted, ref} from 'vue'
import { supabase } from "../supabase"

const listRdv = ref([])

onMounted(async() => {

  const { data: { user } } = await supabase.auth.getUser()
  const { data: rdv } = await supabase
      .from('prise_rdv')
      .select(`
        id,
        date_rdv,
        heure_rdv,
        clients!inner (
          email_user,
          first_name,
          last_name,
          address_latitude,
          address_longitude,
          address,
          address_codepostal,
          address_ville
        ),
        prestataires!inner (
          first_name,
          last_name,
          email_user
        )
      `)
      .eq('prestataires.email_user', user?.email)



  console.log(rdv)

  let lat = 45.764043
  let lon = 4.835659
  let macarte = null

  macarte = L.map('map').setView([lat, lon], 11)
  L.tileLayer('https://{s}.tile.openstreetmap.fr/osmfr/{z}/{x}/{y}.png').addTo(macarte)
  rdv?.forEach(function(element){
    let marker = L.marker([element.clients.address_latitude, element.clients.address_longitude]).addTo(macarte)
    marker.bindPopup(
        'Adresse de ' + element.clients.first_name + ' ' + element.clients.last_name + ' : <br>' +
                element.clients.address + ' ' + element.clients.address_codepostal + ' ' + element.clients.address_ville
    )
  })
  L.control.locate().addTo(macarte);

})

</script>

<template>
  <h1>Liste des clients aux alentours</h1>

  <div id="map"></div>
</template>

<style scoped>
#map{
  height:400px;
}
</style>