<template>
  <div class="min-h-screen bg-gray-50">
    <!-- Login Form (wenn nicht eingeloggt) -->
    <div v-if="!isAuthenticated" class="min-h-screen flex items-center justify-center py-12 px-4 sm:px-6 lg:px-8">
      <div class="max-w-md w-full space-y-8">
        <div>
          <h2 class="mt-6 text-center text-3xl font-extrabold text-gray-900">
            Admin Login
          </h2>
        </div>
        <form @submit.prevent="login" class="mt-8 space-y-6">
          <div class="rounded-md shadow-sm -space-y-px">
            <div>
              <label for="username" class="sr-only">Benutzername</label>
              <input
                id="username"
                v-model="loginForm.username"
                name="username"
                type="text"
                required
                class="appearance-none rounded-none relative block w-full px-3 py-2 border border-gray-300 placeholder-gray-500 text-gray-900 rounded-t-md focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 focus:z-10 sm:text-sm"
                placeholder="Benutzername"
              />
            </div>
            <div>
              <label for="password" class="sr-only">Passwort</label>
              <input
                id="password"
                v-model="loginForm.password"
                name="password"
                type="password"
                required
                class="appearance-none rounded-none relative block w-full px-3 py-2 border border-gray-300 placeholder-gray-500 text-gray-900 rounded-b-md focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 focus:z-10 sm:text-sm"
                placeholder="Passwort"
              />
            </div>
          </div>

          <div v-if="loginError" class="text-red-600 text-sm text-center">
            {{ loginError }}
          </div>

          <div>
            <button
              type="submit"
              :disabled="isLoggingIn"
              class="group relative w-full flex justify-center py-2 px-4 border border-transparent text-sm font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 disabled:opacity-50"
            >
              {{ isLoggingIn ? 'Anmelden...' : 'Anmelden' }}
            </button>
          </div>
        </form>
      </div>
    </div>

    <!-- Admin Dashboard (wenn eingeloggt) -->
    <div v-else class="py-8">
      <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
        <div class="bg-white shadow rounded-lg">
          <div class="px-4 py-5 sm:p-6">
            <div class="flex justify-between items-center mb-8">
              <h1 class="text-2xl font-bold text-gray-900">Admin Portal</h1>
              <button
                @click="logout"
                class="bg-red-600 text-white px-4 py-2 rounded-md hover:bg-red-700"
              >
                Abmelden
              </button>
            </div>
            
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
                    <!-- Grunddaten -->
                    <div>
                      <label class="block text-sm font-medium text-gray-700 mb-2">Titel *</label>
                      <input
                        v-model="newProperty.title"
                        type="text"
                        required
                        class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500"
                        placeholder="z.B. Moderne Eigentumswohnung"
                      />
                    </div>

                    <div>
                      <label class="block text-sm font-medium text-gray-700 mb-2">Typ *</label>
                      <select
                        v-model="newProperty.type"
                        required
                        class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500"
                      >
                        <option value="">Bitte wählen</option>
                        <option value="Eigentumswohnung">Eigentumswohnung</option>
                        <option value="Einfamilienhaus">Einfamilienhaus</option>
                        <option value="Mehrfamilienhaus">Mehrfamilienhaus</option>
                        <option value="Penthouse">Penthouse</option>
                        <option value="Maisonette">Maisonette</option>
                        <option value="Villa">Villa</option>
                        <option value="Gewerbeimmobilie">Gewerbeimmobilie</option>
                        <option value="Bürogebäude">Bürogebäude</option>
                      </select>
                    </div>

                    <div>
                      <label class="block text-sm font-medium text-gray-700 mb-2">Standort *</label>
                      <input
                        v-model="newProperty.location"
                        type="text"
                        required
                        class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500"
                        placeholder="z.B. München-Schwabing"
                      />
                    </div>

                    <div class="grid grid-cols-2 gap-4">
                      <div>
                        <label class="block text-sm font-medium text-gray-700 mb-2">Preis (€) *</label>
                        <input
                          v-model="newProperty.price"
                          type="text"
                          required
                          class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500"
                          placeholder="850.000"
                        />
                      </div>
                      <div>
                        <label class="block text-sm font-medium text-gray-700 mb-2">Größe (m²) *</label>
                        <input
                          v-model="newProperty.size"
                          type="text"
                          required
                          class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500"
                          placeholder="95"
                        />
                      </div>
                    </div>

                    <div>
                      <label class="block text-sm font-medium text-gray-700 mb-2">Zimmer *</label>
                      <input
                        v-model="newProperty.rooms"
                        type="text"
                        required
                        class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500"
                        placeholder="3.5"
                      />
                    </div>

                    <div>
                      <label class="block text-sm font-medium text-gray-700 mb-2">Beschreibung</label>
                      <textarea
                        v-model="newProperty.description"
                        rows="3"
                        class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500"
                        placeholder="Beschreibung der Immobilie..."
                      ></textarea>
                    </div>

                    <!-- Ausstattung Section -->
                    <div class="border-t pt-6">
                      <h3 class="text-lg font-medium text-gray-900 mb-4">🏠 Ausstattung</h3>
                      
                      <!-- Grundausstattung -->
                      <div class="mb-6">
                        <h4 class="text-sm font-medium text-gray-700 mb-3">Grundausstattung</h4>
                        <div class="grid grid-cols-2 gap-3">
                          <label class="flex items-center">
                            <input
                              v-model="newProperty.features.kitchen"
                              type="checkbox"
                              class="rounded border-gray-300 text-indigo-600 focus:ring-indigo-500"
                            />
                            <span class="ml-2 text-sm text-gray-700">🍳 Einbauküche</span>
                          </label>
                          <label class="flex items-center">
                            <input
                              v-model="newProperty.features.balcony"
                              type="checkbox"
                              class="rounded border-gray-300 text-indigo-600 focus:ring-indigo-500"
                            />
                            <span class="ml-2 text-sm text-gray-700">🌿 Balkon/Terrasse</span>
                          </label>
                          <label class="flex items-center">
                            <input
                              v-model="newProperty.features.garden"
                              type="checkbox"
                              class="rounded border-gray-300 text-indigo-600 focus:ring-indigo-500"
                            />
                            <span class="ml-2 text-sm text-gray-700">🌳 Garten</span>
                          </label>
                          <label class="flex items-center">
                            <input
                              v-model="newProperty.features.elevator"
                              type="checkbox"
                              class="rounded border-gray-300 text-indigo-600 focus:ring-indigo-500"
                            />
                            <span class="ml-2 text-sm text-gray-700">🛗 Aufzug</span>
                          </label>
                          <label class="flex items-center">
                            <input
                              v-model="newProperty.features.basement"
                              type="checkbox"
                              class="rounded border-gray-300 text-indigo-600 focus:ring-indigo-500"
                            />
                            <span class="ml-2 text-sm text-gray-700">🏠 Keller</span>
                          </label>
                          <label class="flex items-center">
                            <input
                              v-model="newProperty.features.furnished"
                              type="checkbox"
                              class="rounded border-gray-300 text-indigo-600 focus:ring-indigo-500"
                            />
                            <span class="ml-2 text-sm text-gray-700">🛋️ Möbliert</span>
                          </label>
                          <label class="flex items-center">
                            <input
                              v-model="newProperty.features.floorHeating"
                              type="checkbox"
                              class="rounded border-gray-300 text-indigo-600 focus:ring-indigo-500"
                            />
                            <span class="ml-2 text-sm text-gray-700">🔥 Fußbodenheizung</span>
                          </label>
                          <label class="flex items-center">
                            <input
                              v-model="newProperty.features.airConditioning"
                              type="checkbox"
                              class="rounded border-gray-300 text-indigo-600 focus:ring-indigo-500"
                            />
                            <span class="ml-2 text-sm text-gray-700">❄️ Klimaanlage</span>
                          </label>
                          <label class="flex items-center">
                            <input
                              v-model="newProperty.features.fireplace"
                              type="checkbox"
                              class="rounded border-gray-300 text-indigo-600 focus:ring-indigo-500"
                            />
                            <span class="ml-2 text-sm text-gray-700">🔥 Kamin</span>
                          </label>
                          <label class="flex items-center">
                            <input
                              v-model="newProperty.features.pool"
                              type="checkbox"
                              class="rounded border-gray-300 text-indigo-600 focus:ring-indigo-500"
                            />
                            <span class="ml-2 text-sm text-gray-700">🏊 Pool</span>
                          </label>
                        </div>
                      </div>

                      <!-- Parkplatz -->
                      <div class="mb-6">
                        <h4 class="text-sm font-medium text-gray-700 mb-3">🚗 Parkplatz</h4>
                        <select
                          v-model="newProperty.features.parking"
                          class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500"
                        >
                          <option value="">Kein Parkplatz</option>
                          <option value="Tiefgarage">Tiefgarage</option>
                          <option value="Garage">Garage</option>
                          <option value="Carport">Carport</option>
                          <option value="Stellplatz">Stellplatz</option>
                          <option value="Straßenparkplatz">Straßenparkplatz</option>
                        </select>
                      </div>

                      <!-- Heizung -->
                      <div class="mb-6">
                        <h4 class="text-sm font-medium text-gray-700 mb-3">🔥 Heizung</h4>
                        <select
                          v-model="newProperty.features.heating"
                          class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500"
                        >
                          <option value="">Nicht angegeben</option>
                          <option value="Gasheizung">Gasheizung</option>
                          <option value="Fernwärme">Fernwärme</option>
                          <option value="Ölheizung">Ölheizung</option>
                          <option value="Wärmepumpe">Wärmepumpe</option>
                          <option value="Fußbodenheizung">Fußbodenheizung</option>
                          <option value="Elektroheizung">Elektroheizung</option>
                          <option value="Pelletheizung">Pelletheizung</option>
                        </select>
                      </div>

                      <!-- Baujahr und Zustand -->
                      <div class="grid grid-cols-2 gap-4 mb-6">
                        <div>
                          <h4 class="text-sm font-medium text-gray-700 mb-3">🏗️ Baujahr</h4>
                          <input
                            v-model="newProperty.details.yearBuilt"
                            type="number"
                            min="1800"
                            :max="new Date().getFullYear()"
                            class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500"
                            placeholder="2020"
                          />
                        </div>
                        <div>
                          <h4 class="text-sm font-medium text-gray-700 mb-3">✨ Zustand</h4>
                          <select
                            v-model="newProperty.details.condition"
                            class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500"
                          >
                            <option value="">Nicht angegeben</option>
                            <option value="Neubau">Neubau</option>
                            <option value="Neuwertig">Neuwertig</option>
                            <option value="Sehr gut">Sehr gut</option>
                            <option value="Gut">Gut</option>
                            <option value="Renoviert">Renoviert</option>
                            <option value="Renovierungsbedürftig">Renovierungsbedürftig</option>
                          </select>
                        </div>
                      </div>

                      <!-- Zusätzliche Ausstattung -->
                      <div class="mb-6">
                        <h4 class="text-sm font-medium text-gray-700 mb-3">➕ Zusätzliche Ausstattung</h4>
                        <div class="space-y-2">
                          <div v-for="(feature, index) in newProperty.additionalFeatures" :key="index" class="flex items-center space-x-2">
                            <input
                              v-model="newProperty.additionalFeatures[index]"
                              type="text"
                              placeholder="z.B. Sauna, Schwimmbad, Smart Home..."
                              class="flex-1 px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500"
                            />
                            <button
                              @click="removeAdditionalFeature(index)"
                              type="button"
                              class="text-red-600 hover:text-red-800 px-2 py-1"
                            >
                              ❌
                            </button>
                          </div>
                          <button
                            @click="addAdditionalFeature"
                            type="button"
                            class="text-indigo-600 hover:text-indigo-800 text-sm font-medium"
                          >
                            + Weitere Ausstattung hinzufügen
                          </button>
                        </div>
                      </div>
                    </div>

                    <!-- Bilder -->
                    <div>
                      <label class="block text-sm font-medium text-gray-700 mb-2">Bild URLs (eine pro Zeile)</label>
                      <textarea
                        v-model="newProperty.imageUrls"
                        rows="3"
                        class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500"
                        placeholder="/WhatsApp Image 2025-09-01 at 21.35.38 (1).jpeg"
                      ></textarea>
                    </div>

                    <!-- Kontakt -->
                    <div class="border-t pt-6">
                      <h3 class="text-lg font-medium text-gray-900 mb-4">📞 Kontaktdaten</h3>
                      <div class="grid grid-cols-2 gap-4">
                        <div>
                          <label class="block text-sm font-medium text-gray-700 mb-2">E-Mail</label>
                          <input
                            v-model="newProperty.contact.email"
                            type="email"
                            class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500"
                            placeholder="info@immobilienmakler.de"
                          />
                        </div>
                        <div>
                          <label class="block text-sm font-medium text-gray-700 mb-2">Telefon</label>
                          <input
                            v-model="newProperty.contact.phone"
                            type="tel"
                            class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500"
                            placeholder="+49 89 123 456"
                          />
                        </div>
                      </div>
                    </div>

                    <button
                      type="submit"
                      :disabled="isSubmitting"
                      class="w-full bg-indigo-600 text-white py-3 px-4 rounded-md hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-indigo-500 disabled:opacity-50 font-medium"
                    >
                      {{ isSubmitting ? 'Wird hinzugefügt...' : '🏠 Immobilie hinzufügen' }}
                    </button>
                  </form>
                </div>

                <!-- Properties List -->
                <div>
                  <h2 class="text-lg font-medium text-gray-900 mb-4">Vorhandene Immobilien</h2>
                  <div v-if="propertiesLoading" class="text-center py-4">
                    <div class="inline-block animate-spin rounded-full h-6 w-6 border-b-2 border-indigo-600"></div>
                    <p class="mt-2 text-sm text-gray-600">Lade Immobilien...</p>
                  </div>
                  <div v-else-if="propertiesError" class="text-red-600 text-center py-4">
                    {{ propertiesError }}
                  </div>
                  <div v-else class="space-y-4 max-h-96 overflow-y-auto">
                    <div
                      v-for="property in properties"
                      :key="property.id"
                      class="border border-gray-200 rounded-lg p-4 hover:shadow-md transition-shadow"
                    >
                      <div class="flex justify-between items-start">
                        <div class="flex-1">
                          <h3 class="font-medium text-gray-900">{{ property.title }}</h3>
                          <p class="text-sm text-gray-600">{{ property.type }} • {{ property.location }}</p>
                          <p class="text-sm text-gray-600">€{{ property.price }} • {{ property.size }}m² • {{ property.rooms }} Zimmer</p>
                          <div v-if="property.features" class="mt-2 flex flex-wrap gap-1">
                            <span v-if="property.features.includes('Hochwertige Einbauküche')" class="inline-block bg-blue-100 text-blue-800 text-xs px-2 py-1 rounded">🍳 Küche</span>
                            <span v-if="property.features.includes('Balkon mit Südausrichtung')" class="inline-block bg-green-100 text-green-800 text-xs px-2 py-1 rounded">🌿 Balkon</span>
                            <span v-if="property.features.includes('Tiefgaragenstellplatz')" class="inline-block bg-purple-100 text-purple-800 text-xs px-2 py-1 rounded">🚗 Parkplatz</span>
                          </div>
                        </div>
                        <button
                          @click="deleteProperty(property.id)"
                          class="text-red-600 hover:text-red-800 ml-4"
                        >
                          🗑️ Löschen
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
                      <label class="block text-sm font-medium text-gray-700 mb-2">Name *</label>
                      <input
                        v-model="newCompany.name"
                        type="text"
                        required
                        class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500"
                        placeholder="TechStart GmbH"
                      />
                    </div>

                    <div>
                      <label class="block text-sm font-medium text-gray-700 mb-2">Branche *</label>
                      <input
                        v-model="newCompany.industry"
                        type="text"
                        required
                        class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500"
                        placeholder="Software Development"
                      />
                    </div>

                    <div>
                      <label class="block text-sm font-medium text-gray-700 mb-2">Standort *</label>
                      <input
                        v-model="newCompany.location"
                        type="text"
                        required
                        class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500"
                        placeholder="München-Maxvorstadt"
                      />
                    </div>

                    <div class="grid grid-cols-2 gap-4">
                      <div>
                        <label class="block text-sm font-medium text-gray-700 mb-2">Mitarbeiter</label>
                        <input
                          v-model="newCompany.employees"
                          type="text"
                          class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500"
                          placeholder="25-50"
                        />
                      </div>
                      <div>
                        <label class="block text-sm font-medium text-gray-700 mb-2">Gegründet</label>
                        <input
                          v-model="newCompany.founded"
                          type="text"
                          class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500"
                          placeholder="2019"
                        />
                      </div>
                    </div>

                    <div>
                      <label class="block text-sm font-medium text-gray-700 mb-2">Umsatz</label>
                      <input
                        v-model="newCompany.revenue"
                        type="text"
                        class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500"
                        placeholder="2.5M"
                      />
                    </div>

                    <div>
                      <label class="block text-sm font-medium text-gray-700 mb-2">Beschreibung</label>
                      <textarea
                        v-model="newCompany.description"
                        rows="3"
                        class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500"
                        placeholder="Beschreibung des Unternehmens..."
                      ></textarea>
                    </div>

                    <div>
                      <label class="block text-sm font-medium text-gray-700 mb-2">Bild URLs (eine pro Zeile)</label>
                      <textarea
                        v-model="newCompany.imageUrls"
                        rows="2"
                        class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500"
                        placeholder="/WhatsApp Image 2025-09-01 at 21.28.14.jpeg"
                      ></textarea>
                    </div>

                    <button
                      type="submit"
                      :disabled="isSubmitting"
                      class="w-full bg-indigo-600 text-white py-3 px-4 rounded-md hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-indigo-500 disabled:opacity-50 font-medium"
                    >
                      {{ isSubmitting ? 'Wird hinzugefügt...' : '🏢 Unternehmen hinzufügen' }}
                    </button>
                  </form>
                </div>

                <!-- Companies List -->
                <div>
                  <h2 class="text-lg font-medium text-gray-900 mb-4">Vorhandene Unternehmen</h2>
                  <div v-if="companiesLoading" class="text-center py-4">
                    <div class="inline-block animate-spin rounded-full h-6 w-6 border-b-2 border-indigo-600"></div>
                    <p class="mt-2 text-sm text-gray-600">Lade Unternehmen...</p>
                  </div>
                  <div v-else-if="companiesError" class="text-red-600 text-center py-4">
                    {{ companiesError }}
                  </div>
                  <div v-else class="space-y-4 max-h-96 overflow-y-auto">
                    <div
                      v-for="company in companies"
                      :key="company.id"
                      class="border border-gray-200 rounded-lg p-4 hover:shadow-md transition-shadow"
                    >
                      <div class="flex justify-between items-start">
                        <div class="flex-1">
                          <h3 class="font-medium text-gray-900">{{ company.name }}</h3>
                          <p class="text-sm text-gray-600">{{ company.industry }} • {{ company.location }}</p>
                          <p class="text-sm text-gray-600">{{ company.employees }} Mitarbeiter • Gegründet {{ company.founded }}</p>
                        </div>
                        <button
                          @click="deleteCompany(company.id)"
                          class="text-red-600 hover:text-red-800 ml-4"
                        >
                          🗑️ Löschen
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
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
import { authService } from '../services/auth'
import { useProperties, useCompanies } from '../composables/useData'

// Authentication
const isAuthenticated = ref(false)
const isLoggingIn = ref(false)
const loginError = ref('')
const loginForm = ref({
  username: '',
  password: ''
})

// Data composables
const { 
  properties, 
  loading: propertiesLoading, 
  error: propertiesError, 
  addProperty: addPropertyToAPI, 
  deleteProperty: deletePropertyFromAPI 
} = useProperties()

const { 
  companies, 
  loading: companiesLoading, 
  error: companiesError, 
  addCompany: addCompanyToAPI, 
  deleteCompany: deleteCompanyFromAPI 
} = useCompanies()

// UI State
const activeTab = ref('properties')
const isSubmitting = ref(false)

// Property Form
const newProperty = ref({
  title: '',
  type: '',
  location: '',
  price: '',
  size: '',
  rooms: '',
  description: '',
  features: {
    kitchen: false,
    balcony: false,
    garden: false,
    elevator: false,
    basement: false,
    furnished: false,
    floorHeating: false,
    airConditioning: false,
    fireplace: false,
    pool: false,
    parking: '',
    heating: ''
  },
  details: {
    yearBuilt: '',
    condition: ''
  },
  additionalFeatures: [''],
  imageUrls: '',
  contact: {
    email: 'info@immobilienmakler.de',
    phone: '+49 89 123 456'
  }
})

// Company Form
const newCompany = ref({
  name: '',
  industry: '',
  location: '',
  employees: '',
  founded: '',
  revenue: '',
  description: '',
  imageUrls: ''
})

// Authentication Methods
const login = async () => {
  isLoggingIn.value = true
  loginError.value = ''
  
  try {
    await authService.login(loginForm.value)
    isAuthenticated.value = true
  } catch (error) {
    loginError.value = error instanceof Error ? error.message : 'Login fehlgeschlagen'
  } finally {
    isLoggingIn.value = false
  }
}

const logout = () => {
  authService.logout()
  isAuthenticated.value = false
  loginForm.value = { username: '', password: '' }
}

// Property Methods
const addProperty = async () => {
  isSubmitting.value = true
  
  try {
    // Build features array
    const features = []
    if (newProperty.value.features.kitchen) features.push('Hochwertige Einbauküche')
    if (newProperty.value.features.balcony) features.push('Balkon mit Südausrichtung')
    if (newProperty.value.features.garden) features.push('Großer Garten')
    if (newProperty.value.features.elevator) features.push('Aufzug vorhanden')
    if (newProperty.value.features.basement) features.push('Keller')
    if (newProperty.value.features.furnished) features.push('Möbliert')
    if (newProperty.value.features.floorHeating) features.push('Fußbodenheizung')
    if (newProperty.value.features.airConditioning) features.push('Klimaanlage')
    if (newProperty.value.features.fireplace) features.push('Kamin')
    if (newProperty.value.features.pool) features.push('Pool')
    if (newProperty.value.features.parking) features.push(`Parkplatz: ${newProperty.value.features.parking}`)
    if (newProperty.value.features.heating) features.push(`Heizung: ${newProperty.value.features.heating}`)
    
    // Add additional features
    newProperty.value.additionalFeatures.forEach(feature => {
      if (feature.trim()) features.push(feature.trim())
    })

    // Parse images
    const images = newProperty.value.imageUrls
      .split('\n')
      .map(url => url.trim())
      .filter(url => url.length > 0)

    const propertyData = {
      title: newProperty.value.title,
      type: newProperty.value.type,
      location: newProperty.value.location,
      price: newProperty.value.price,
      size: newProperty.value.size,
      rooms: newProperty.value.rooms,
      description: newProperty.value.description,
      features,
      images,
      contact: newProperty.value.contact,
      details: {
        ...newProperty.value.details,
        parking: newProperty.value.features.parking,
        heating: newProperty.value.features.heating,
        balcony: newProperty.value.features.balcony,
        garden: newProperty.value.features.garden,
        elevator: newProperty.value.features.elevator
      }
    }

    await addPropertyToAPI(propertyData)
    resetPropertyForm()
    alert('Immobilie erfolgreich hinzugefügt!')
  } catch (error) {
    console.error('Error adding property:', error)
    alert('Fehler beim Hinzufügen der Immobilie')
  } finally {
    isSubmitting.value = false
  }
}

const deleteProperty = async (id: number) => {
  if (confirm('Sind Sie sicher, dass Sie diese Immobilie löschen möchten?')) {
    try {
      await deletePropertyFromAPI(id)
      alert('Immobilie erfolgreich gelöscht!')
    } catch (error) {
      console.error('Error deleting property:', error)
      alert('Fehler beim Löschen der Immobilie')
    }
  }
}

// Company Methods
const addCompany = async () => {
  isSubmitting.value = true
  
  try {
    const images = newCompany.value.imageUrls
      .split('\n')
      .map(url => url.trim())
      .filter(url => url.length > 0)

    const companyData = {
      name: newCompany.value.name,
      industry: newCompany.value.industry,
      location: newCompany.value.location,
      employees: newCompany.value.employees,
      founded: newCompany.value.founded,
      revenue: newCompany.value.revenue,
      description: newCompany.value.description,
      images,
      highlights: [],
      contact: {
        email: 'info@immobilienmakler.de',
        phone: '+49 89 123 456'
      },
      details: {}
    }

    await addCompanyToAPI(companyData)
    resetCompanyForm()
    alert('Unternehmen erfolgreich hinzugefügt!')
  } catch (error) {
    console.error('Error adding company:', error)
    alert('Fehler beim Hinzufügen des Unternehmens')
  } finally {
    isSubmitting.value = false
  }
}

const deleteCompany = async (id: number) => {
  if (confirm('Sind Sie sicher, dass Sie dieses Unternehmen löschen möchten?')) {
    try {
      await deleteCompanyFromAPI(id)
      alert('Unternehmen erfolgreich gelöscht!')
    } catch (error) {
      console.error('Error deleting company:', error)
      alert('Fehler beim Löschen des Unternehmens')
    }
  }
}

// Helper Methods
const addAdditionalFeature = () => {
  newProperty.value.additionalFeatures.push('')
}

const removeAdditionalFeature = (index: number) => {
  newProperty.value.additionalFeatures.splice(index, 1)
}

const resetPropertyForm = () => {
  newProperty.value = {
    title: '',
    type: '',
    location: '',
    price: '',
    size: '',
    rooms: '',
    description: '',
    features: {
      kitchen: false,
      balcony: false,
      garden: false,
      elevator: false,
      basement: false,
      furnished: false,
      floorHeating: false,
      airConditioning: false,
      fireplace: false,
      pool: false,
      parking: '',
      heating: ''
    },
    details: {
      yearBuilt: '',
      condition: ''
    },
    additionalFeatures: [''],
    imageUrls: '',
    contact: {
      email: 'info@immobilienmakler.de',
      phone: '+49 89 123 456'
    }
  }
}

const resetCompanyForm = () => {
  newCompany.value = {
    name: '',
    industry: '',
    location: '',
    employees: '',
    founded: '',
    revenue: '',
    description: '',
    imageUrls: ''
  }
}

// Lifecycle
onMounted(async () => {
  // Check if already authenticated
  const isValid = await authService.verifyToken()
  isAuthenticated.value = isValid
})
</script>