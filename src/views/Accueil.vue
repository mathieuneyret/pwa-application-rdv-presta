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
          email_user
        ),
        prestataires!inner (
          first_name,
          last_name,
          email_user
        )
      `)
      .eq('prestataires.email_user', user?.email)

  rdv?.forEach(element => console.log(element.date_rdv))

  console.log(rdv)

  let lat = 45.764043
  let lon = 4.835659
  let macarte = null

  macarte = L.map('map').setView([lat, lon], 11)
  L.tileLayer('https://{s}.tile.openstreetmap.fr/osmfr/{z}/{x}/{y}.png').addTo(macarte)
  let marker = L.marker([45.750165,4.749065]).addTo(macarte)
              .bindPopup('Mon adresse')
              .openPopup()
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