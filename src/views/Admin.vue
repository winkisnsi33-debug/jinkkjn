<template>
  <div class="min-h-screen bg-gray-50 py-8">
    <div class="max-w-4xl mx-auto px-4 sm:px-6 lg:px-8">
      <div class="bg-white shadow rounded-lg">
        <div class="px-4 py-5 sm:p-6">
          <h1 class="text-2xl font-bold text-gray-900 mb-8">Admin Portal</h1>
          
          <!-- Navigation Tabs -->
          <div class="border-b border-gray-200 mb-6">
            <nav class="-mb-px flex space-x-8">
              <button
                @click="activeTab = 'properties'"
                :class="[
                  'py-2 px-1 border-b-2 font-medium text-sm',
                  activeTab === 'properties'
                    ? 'border-indigo-500 text-indigo-600'
                    : 'border-transparent text-gray-500 hover:text-gray-700 hover:border-gray-300'
                ]"
              >
                Immobilien verwalten
              </button>
              <button
                @click="activeTab = 'companies'"
                :class="[
                  'py-2 px-1 border-b-2 font-medium text-sm',
                  activeTab === 'companies'
                    ? 'border-indigo-500 text-indigo-600'
                    : 'border-transparent text-gray-500 hover:text-gray-700 hover:border-gray-300'
                ]"
              >
                Unternehmen verwalten
              </button>
            </nav>
          </div>

          <!-- Properties Tab -->
          <div v-if="activeTab === 'properties'">
            <div class="grid grid-cols-1 lg:grid-cols-2 gap-8">
              <!-- Add Property Form -->
              <div>
                <h2 class="text-lg font-medium text-gray-900 mb-4">Neue Immobilie hinzufügen</h2>
                <form @submit.prevent="addProperty" class="space-y-4">
                  <div>
                    <label class="block text-sm font-medium text-gray-700">Titel</label>
                    <input
                      v-model="newProperty.title"
                      type="text"
                      required
                      class="mt-1 block w-full border-gray-300 rounded-md shadow-sm focus:ring-indigo-500 focus:border-indigo-500"
                    />
                  </div>

                  <div>
                    <label class="block text-sm font-medium text-gray-700">Beschreibung</label>
                    <textarea
                      v-model="newProperty.description"
                      rows="3"
                      class="mt-1 block w-full border-gray-300 rounded-md shadow-sm focus:ring-indigo-500 focus:border-indigo-500"
                    ></textarea>
                  </div>

                  <div class="grid grid-cols-2 gap-4">
                    <div>
                      <label class="block text-sm font-medium text-gray-700">Preis (€)</label>
                      <input
                        v-model.number="newProperty.price"
                        type="number"
                        required
                        class="mt-1 block w-full border-gray-300 rounded-md shadow-sm focus:ring-indigo-500 focus:border-indigo-500"
                      />
                    </div>
                    <div>
                      <label class="block text-sm font-medium text-gray-700">Größe (m²)</label>
                      <input
                        v-model.number="newProperty.size"
                        type="number"
                        required
                        class="mt-1 block w-full border-gray-300 rounded-md shadow-sm focus:ring-indigo-500 focus:border-indigo-500"
                      />
                    </div>
                  </div>

                  <div class="grid grid-cols-3 gap-4">
                    <div>
                      <label class="block text-sm font-medium text-gray-700">Zimmer</label>
                      <input
                        v-model.number="newProperty.rooms"
                        type="number"
                        required
                        class="mt-1 block w-full border-gray-300 rounded-md shadow-sm focus:ring-indigo-500 focus:border-indigo-500"
                      />
                    </div>
                    <div>
                      <label class="block text-sm font-medium text-gray-700">Badezimmer</label>
                      <input
                        v-model.number="newProperty.bathrooms"
                        type="number"
                        required
                        class="mt-1 block w-full border-gray-300 rounded-md shadow-sm focus:ring-indigo-500 focus:border-indigo-500"
                      />
                    </div>
                    <div>
                      <label class="block text-sm font-medium text-gray-700">Baujahr</label>
                      <input
                        v-model.number="newProperty.yearBuilt"
                        type="number"
                        class="mt-1 block w-full border-gray-300 rounded-md shadow-sm focus:ring-indigo-500 focus:border-indigo-500"
                      />
                    </div>
                  </div>

                  <div>
                    <label class="block text-sm font-medium text-gray-700">Adresse</label>
                    <input
                      v-model="newProperty.location"
                      type="text"
                      required
                      class="mt-1 block w-full border-gray-300 rounded-md shadow-sm focus:ring-indigo-500 focus:border-indigo-500"
                    />
                  </div>

                  <div>
                    <label class="block text-sm font-medium text-gray-700">Typ</label>
                    <select
                      v-model="newProperty.type"
                      class="mt-1 block w-full border-gray-300 rounded-md shadow-sm focus:ring-indigo-500 focus:border-indigo-500"
                    >
                      <option value="apartment">Wohnung</option>
                      <option value="house">Haus</option>
                      <option value="commercial">Gewerbe</option>
                    </select>
                  </div>

                  <div>
                    <label class="block text-sm font-medium text-gray-700">Zustand</label>
                    <select
                      v-model="newProperty.condition"
                      class="mt-1 block w-full border-gray-300 rounded-md shadow-sm focus:ring-indigo-500 focus:border-indigo-500"
                    >
                      <option value="new">Neubau</option>
                      <option value="like-new">Neuwertig</option>
                      <option value="very-good">Sehr gut</option>
                      <option value="good">Gut</option>
                      <option value="renovated">Renoviert</option>
                      <option value="needs-renovation">Renovierungsbedürftig</option>
                    </select>
                  </div>

                  <!-- Ausstattung Section -->
                  <div class="border-t pt-6">
                    <h3 class="text-lg font-medium text-gray-900 mb-4">Ausstattung</h3>
                    
                    <!-- Basic Amenities -->
                    <div class="mb-6">
                      <h4 class="text-sm font-medium text-gray-700 mb-3">Grundausstattung</h4>
                      <div class="grid grid-cols-2 gap-3">
                        <label class="flex items-center">
                          <input
                            v-model="newProperty.amenities.kitchen"
                            type="checkbox"
                            class="rounded border-gray-300 text-indigo-600 focus:ring-indigo-500"
                          />
                          <span class="ml-2 text-sm text-gray-700">Einbauküche</span>
                        </label>
                        <label class="flex items-center">
                          <input
                            v-model="newProperty.amenities.balcony"
                            type="checkbox"
                            class="rounded border-gray-300 text-indigo-600 focus:ring-indigo-500"
                          />
                          <span class="ml-2 text-sm text-gray-700">Balkon/Terrasse</span>
                        </label>
                        <label class="flex items-center">
                          <input
                            v-model="newProperty.amenities.garden"
                            type="checkbox"
                            class="rounded border-gray-300 text-indigo-600 focus:ring-indigo-500"
                          />
                          <span class="ml-2 text-sm text-gray-700">Garten</span>
                        </label>
                        <label class="flex items-center">
                          <input
                            v-model="newProperty.amenities.elevator"
                            type="checkbox"
                            class="rounded border-gray-300 text-indigo-600 focus:ring-indigo-500"
                          />
                          <span class="ml-2 text-sm text-gray-700">Aufzug</span>
                        </label>
                        <label class="flex items-center">
                          <input
                            v-model="newProperty.amenities.basement"
                            type="checkbox"
                            class="rounded border-gray-300 text-indigo-600 focus:ring-indigo-500"
                          />
                          <span class="ml-2 text-sm text-gray-700">Keller</span>
                        </label>
                        <label class="flex items-center">
                          <input
                            v-model="newProperty.amenities.furnished"
                            type="checkbox"
                            class="rounded border-gray-300 text-indigo-600 focus:ring-indigo-500"
                          />
                          <span class="ml-2 text-sm text-gray-700">Möbliert</span>
                        </label>
                        <label class="flex items-center">
                          <input
                            v-model="newProperty.amenities.airConditioning"
                            type="checkbox"
                            class="rounded border-gray-300 text-indigo-600 focus:ring-indigo-500"
                          />
                          <span class="ml-2 text-sm text-gray-700">Klimaanlage</span>
                        </label>
                        <label class="flex items-center">
                          <input
                            v-model="newProperty.amenities.fireplace"
                            type="checkbox"
                            class="rounded border-gray-300 text-indigo-600 focus:ring-indigo-500"
                          />
                          <span class="ml-2 text-sm text-gray-700">Kamin</span>
                        </label>
                      </div>
                    </div>

                    <!-- Parking -->
                    <div class="mb-6">
                      <h4 class="text-sm font-medium text-gray-700 mb-3">Parkplatz</h4>
                      <select
                        v-model="newProperty.amenities.parking"
                        class="block w-full border-gray-300 rounded-md shadow-sm focus:ring-indigo-500 focus:border-indigo-500"
                      >
                        <option value="">Kein Parkplatz</option>
                        <option value="garage">Garage</option>
                        <option value="carport">Carport</option>
                        <option value="parking-space">Stellplatz</option>
                        <option value="underground">Tiefgarage</option>
                        <option value="street">Straßenparkplatz</option>
                      </select>
                    </div>

                    <!-- Heating -->
                    <div class="mb-6">
                      <h4 class="text-sm font-medium text-gray-700 mb-3">Heizung</h4>
                      <select
                        v-model="newProperty.amenities.heating"
                        class="block w-full border-gray-300 rounded-md shadow-sm focus:ring-indigo-500 focus:border-indigo-500"
                      >
                        <option value="">Nicht angegeben</option>
                        <option value="gas">Gasheizung</option>
                        <option value="district">Fernwärme</option>
                        <option value="oil">Ölheizung</option>
                        <option value="heat-pump">Wärmepumpe</option>
                        <option value="floor">Fußbodenheizung</option>
                        <option value="electric">Elektroheizung</option>
                      </select>
                    </div>

                    <!-- Additional Features -->
                    <div class="mb-6">
                      <h4 class="text-sm font-medium text-gray-700 mb-3">Zusätzliche Ausstattung</h4>
                      <div class="space-y-2">
                        <div v-for="(feature, index) in newProperty.amenities.additional" :key="index" class="flex items-center space-x-2">
                          <input
                            v-model="newProperty.amenities.additional[index]"
                            type="text"
                            placeholder="z.B. Sauna, Schwimmbad, etc."
                            class="flex-1 border-gray-300 rounded-md shadow-sm focus:ring-indigo-500 focus:border-indigo-500"
                          />
                          <button
                            @click="removeAdditionalFeature(index)"
                            type="button"
                            class="text-red-600 hover:text-red-800"
                          >
                            Entfernen
                          </button>
                        </div>
                        <button
                          @click="addAdditionalFeature"
                          type="button"
                          class="text-indigo-600 hover:text-indigo-800 text-sm"
                        >
                          + Weitere Ausstattung hinzufügen
                        </button>
                      </div>
                    </div>
                  </div>

                  <div>
                    <label class="block text-sm font-medium text-gray-700">Bild URL</label>
                    <input
                      v-model="newProperty.image"
                      type="url"
                      class="mt-1 block w-full border-gray-300 rounded-md shadow-sm focus:ring-indigo-500 focus:border-indigo-500"
                    />
                  </div>

                  <button
                    type="submit"
                    class="w-full bg-indigo-600 text-white py-2 px-4 rounded-md hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-indigo-500"
                  >
                    Immobilie hinzufügen
                  </button>
                </form>
              </div>

              <!-- Properties List -->
              <div>
                <h2 class="text-lg font-medium text-gray-900 mb-4">Vorhandene Immobilien</h2>
                <div class="space-y-4 max-h-96 overflow-y-auto">
                  <div
                    v-for="property in properties"
                    :key="property.id"
                    class="border border-gray-200 rounded-lg p-4"
                  >
                    <div class="flex justify-between items-start">
                      <div>
                        <h3 class="font-medium text-gray-900">{{ property.title }}</h3>
                        <p class="text-sm text-gray-600">{{ property.location }}</p>
                        <p class="text-sm text-gray-600">{{ property.price?.toLocaleString() }}€</p>
                      </div>
                      <button
                        @click="deleteProperty(property.id)"
                        class="text-red-600 hover:text-red-800"
                      >
                        Löschen
                      </button>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>

          <!-- Companies Tab -->
          <div v-if="activeTab === 'companies'">
            <div class="grid grid-cols-1 lg:grid-cols-2 gap-8">
              <!-- Add Company Form -->
              <div>
                <h2 class="text-lg font-medium text-gray-900 mb-4">Neues Unternehmen hinzufügen</h2>
                <form @submit.prevent="addCompany" class="space-y-4">
                  <div>
                    <label class="block text-sm font-medium text-gray-700">Name</label>
                    <input
                      v-model="newCompany.name"
                      type="text"
                      required
                      class="mt-1 block w-full border-gray-300 rounded-md shadow-sm focus:ring-indigo-500 focus:border-indigo-500"
                    />
                  </div>

                  <div>
                    <label class="block text-sm font-medium text-gray-700">Beschreibung</label>
                    <textarea
                      v-model="newCompany.description"
                      rows="3"
                      class="mt-1 block w-full border-gray-300 rounded-md shadow-sm focus:ring-indigo-500 focus:border-indigo-500"
                    ></textarea>
                  </div>

                  <div>
                    <label class="block text-sm font-medium text-gray-700">Logo URL</label>
                    <input
                      v-model="newCompany.logo"
                      type="url"
                      class="mt-1 block w-full border-gray-300 rounded-md shadow-sm focus:ring-indigo-500 focus:border-indigo-500"
                    />
                  </div>

                  <div>
                    <label class="block text-sm font-medium text-gray-700">Website</label>
                    <input
                      v-model="newCompany.website"
                      type="url"
                      class="mt-1 block w-full border-gray-300 rounded-md shadow-sm focus:ring-indigo-500 focus:border-indigo-500"
                    />
                  </div>

                  <button
                    type="submit"
                    class="w-full bg-indigo-600 text-white py-2 px-4 rounded-md hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-indigo-500"
                  >
                    Unternehmen hinzufügen
                  </button>
                </form>
              </div>

              <!-- Companies List -->
              <div>
                <h2 class="text-lg font-medium text-gray-900 mb-4">Vorhandene Unternehmen</h2>
                <div class="space-y-4 max-h-96 overflow-y-auto">
                  <div
                    v-for="company in companies"
                    :key="company.id"
                    class="border border-gray-200 rounded-lg p-4"
                  >
                    <div class="flex justify-between items-start">
                      <div>
                        <h3 class="font-medium text-gray-900">{{ company.name }}</h3>
                        <p class="text-sm text-gray-600">{{ company.description }}</p>
                      </div>
                      <button
                        @click="deleteCompany(company.id)"
                        class="text-red-600 hover:text-red-800"
                      >
                        Löschen
                      </button>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
import { useData } from '../composables/useData'

const { properties, companies, loadProperties, loadCompanies } = useData()

const activeTab = ref('properties')

// Property form
const newProperty = ref({
  title: '',
  description: '',
  price: 0,
  size: 0,
  rooms: 0,
  bathrooms: 0,
  yearBuilt: new Date().getFullYear(),
  location: '',
  type: 'apartment',
  condition: 'good',
  image: '',
  amenities: {
    kitchen: false,
    balcony: false,
    garden: false,
    elevator: false,
    basement: false,
    furnished: false,
    airConditioning: false,
    fireplace: false,
    parking: '',
    heating: '',
    additional: ['']
  }
})

// Company form
const newCompany = ref({
  name: '',
  description: '',
  logo: '',
  website: ''
})

const addProperty = async () => {
  try {
    const propertyData = {
      ...newProperty.value,
      id: Date.now(),
      details: {
        ...newProperty.value.amenities,
        yearBuilt: newProperty.value.yearBuilt,
        condition: newProperty.value.condition
      }
    }
    
    const response = await fetch('/api/properties', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(propertyData)
    })
    
    if (response.ok) {
      await loadProperties()
      resetPropertyForm()
      alert('Immobilie erfolgreich hinzugefügt!')
    } else {
      throw new Error('Fehler beim Hinzufügen der Immobilie')
    }
  } catch (error) {
    console.error('Error adding property:', error)
    alert('Fehler beim Hinzufügen der Immobilie')
  }
}

const addCompany = async () => {
  try {
    const companyData = {
      ...newCompany.value,
      id: Date.now()
    }
    
    const response = await fetch('/api/companies', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(companyData)
    })
    
    if (response.ok) {
      await loadCompanies()
      resetCompanyForm()
      alert('Unternehmen erfolgreich hinzugefügt!')
    } else {
      throw new Error('Fehler beim Hinzufügen des Unternehmens')
    }
  } catch (error) {
    console.error('Error adding company:', error)
    alert('Fehler beim Hinzufügen des Unternehmens')
  }
}

const deleteProperty = async (id: number) => {
  if (confirm('Sind Sie sicher, dass Sie diese Immobilie löschen möchten?')) {
    try {
      const response = await fetch(`/api/properties/${id}`, {
        method: 'DELETE'
      })
      
      if (response.ok) {
        await loadProperties()
        alert('Immobilie erfolgreich gelöscht!')
      } else {
        throw new Error('Fehler beim Löschen der Immobilie')
      }
    } catch (error) {
      console.error('Error deleting property:', error)
      alert('Fehler beim Löschen der Immobilie')
    }
  }
}

const deleteCompany = async (id: number) => {
  if (confirm('Sind Sie sicher, dass Sie dieses Unternehmen löschen möchten?')) {
    try {
      const response = await fetch(`/api/companies/${id}`, {
        method: 'DELETE'
      })
      
      if (response.ok) {
        await loadCompanies()
        alert('Unternehmen erfolgreich gelöscht!')
      } else {
        throw new Error('Fehler beim Löschen des Unternehmens')
      }
    } catch (error) {
      console.error('Error deleting company:', error)
      alert('Fehler beim Löschen des Unternehmens')
    }
  }
}

const addAdditionalFeature = () => {
  newProperty.value.amenities.additional.push('')
}

const removeAdditionalFeature = (index: number) => {
  newProperty.value.amenities.additional.splice(index, 1)
}

const resetPropertyForm = () => {
  newProperty.value = {
    title: '',
    description: '',
    price: 0,
    size: 0,
    rooms: 0,
    bathrooms: 0,
    yearBuilt: new Date().getFullYear(),
    location: '',
    type: 'apartment',
    condition: 'good',
    image: '',
    amenities: {
      kitchen: false,
      balcony: false,
      garden: false,
      elevator: false,
      basement: false,
      furnished: false,
      airConditioning: false,
      fireplace: false,
      parking: '',
      heating: '',
      additional: ['']
    }
  }
}

const resetCompanyForm = () => {
  newCompany.value = {
    name: '',
    description: '',
    logo: '',
    website: ''
  }
}

onMounted(() => {
  loadProperties()
  loadCompanies()
})
</script>