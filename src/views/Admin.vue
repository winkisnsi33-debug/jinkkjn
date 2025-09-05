<template>
  <div class="min-h-screen bg-gray-50">
    <!-- Login Form -->
    <div v-if="!isAuthenticated" class="min-h-screen flex items-center justify-center">
      <div class="max-w-md w-full bg-white rounded-lg shadow-lg p-8">
        <div class="text-center mb-8">
          <div class="w-16 h-16 bg-blue-600 rounded-full flex items-center justify-center mx-auto mb-4">
            <svg class="w-8 h-8 text-white" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 15v2m-6 4h12a2 2 0 002-2v-6a2 2 0 00-2-2H6a2 2 0 00-2 2v6a2 2 0 002 2zm10-10V7a4 4 0 00-8 0v4h8z" />
            </svg>
          </div>
          <h1 class="text-2xl font-bold text-gray-900">Admin Portal</h1>
          <p class="text-gray-600 mt-2">Melden Sie sich an, um das Admin Panel zu verwenden</p>
        </div>
        
        <form @submit.prevent="handleLogin" class="space-y-6">
          <div>
            <label for="username" class="block text-sm font-medium text-gray-700 mb-2">
              Benutzername
            </label>
            <input
              id="username"
              v-model="loginForm.username"
              type="text"
              required
              class="w-full px-4 py-3 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent transition-colors"
              placeholder="admin"
            />
          </div>
          
          <div>
            <label for="password" class="block text-sm font-medium text-gray-700 mb-2">
              Passwort
            </label>
            <input
              id="password"
              v-model="loginForm.password"
              type="password"
              required
              class="w-full px-4 py-3 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent transition-colors"
              placeholder="Ihr Passwort"
            />
          </div>
          
          <div v-if="loginError" class="bg-red-50 border border-red-200 text-red-600 px-4 py-3 rounded-lg text-sm">
            {{ loginError }}
          </div>
          
          <button
            type="submit"
            :disabled="loginLoading"
            class="w-full bg-blue-600 text-white py-3 px-4 rounded-lg hover:bg-blue-700 disabled:opacity-50 disabled:cursor-not-allowed transition-colors font-medium"
          >
            <span v-if="loginLoading" class="flex items-center justify-center">
              <svg class="animate-spin -ml-1 mr-3 h-5 w-5 text-white" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
                <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
              </svg>
              Anmelden...
            </span>
            <span v-else>Anmelden</span>
          </button>
        </form>
        
        <div class="mt-6 p-4 bg-blue-50 rounded-lg border border-blue-200">
          <p class="text-sm text-blue-800 text-center">
            <strong>Demo-Zugangsdaten:</strong><br>
            Benutzername: <code class="bg-blue-100 px-1 rounded">admin</code><br>
            Passwort: <code class="bg-blue-100 px-1 rounded">password</code>
          </p>
        </div>
      </div>
    </div>

    <!-- Admin Panel -->
    <div v-else class="admin-container">
      <!-- Header -->
      <div class="bg-white shadow-sm border-b border-gray-200 mb-8">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
          <div class="flex justify-between items-center py-6">
            <div>
              <h1 class="text-3xl font-bold text-gray-900">Admin Portal</h1>
              <p class="text-gray-600 mt-1">Verwalten Sie Immobilien und Unternehmen</p>
            </div>
            <div class="flex items-center space-x-4">
              <div class="text-sm text-gray-500">
                Angemeldet als <span class="font-medium">Administrator</span>
              </div>
              <button
                @click="handleLogout"
                class="bg-red-600 text-white px-4 py-2 rounded-lg hover:bg-red-700 transition-colors font-medium"
              >
                Abmelden
              </button>
            </div>
          </div>
        </div>
      </div>

      <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
        <!-- Statistics Cards -->
        <div class="grid grid-cols-1 md:grid-cols-4 gap-6 mb-8">
          <div class="bg-white rounded-lg shadow-sm p-6 border border-gray-200">
            <div class="flex items-center">
              <div class="p-3 rounded-full bg-blue-100 text-blue-600 mr-4">
                <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 21V5a2 2 0 00-2-2H7a2 2 0 00-2 2v16m14 0h2m-2 0h-5m-9 0H3m2 0h5M9 7h1m-1 4h1m4-4h1m-1 4h1m-5 10v-5a1 1 0 011-1h2a1 1 0 011 1v5m-4 0h4"></path>
                </svg>
              </div>
              <div>
                <p class="text-2xl font-bold text-gray-900">{{ properties.length }}</p>
                <p class="text-gray-600">Immobilien</p>
              </div>
            </div>
          </div>
          
          <div class="bg-white rounded-lg shadow-sm p-6 border border-gray-200">
            <div class="flex items-center">
              <div class="p-3 rounded-full bg-green-100 text-green-600 mr-4">
                <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 21V5a2 2 0 00-2-2H7a2 2 0 00-2 2v16m14 0h2m-2 0h-5m-9 0H3m2 0h5M9 7h1m-1 4h1m4-4h1m-1 4h1m-5 10v-5a1 1 0 011-1h2a1 1 0 011 1v5m-4 0h4"></path>
                </svg>
              </div>
              <div>
                <p class="text-2xl font-bold text-gray-900">{{ companies.length }}</p>
                <p class="text-gray-600">Unternehmen</p>
              </div>
            </div>
          </div>
          
          <div class="bg-white rounded-lg shadow-sm p-6 border border-gray-200">
            <div class="flex items-center">
              <div class="p-3 rounded-full bg-purple-100 text-purple-600 mr-4">
                <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z"></path>
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M2.458 12C3.732 7.943 7.523 5 12 5c4.478 0 8.268 2.943 9.542 7-1.274 4.057-5.064 7-9.542 7-4.477 0-8.268-2.943-9.542-7z"></path>
                </svg>
              </div>
              <div>
                <p class="text-2xl font-bold text-gray-900">{{ totalViews }}</p>
                <p class="text-gray-600">Aufrufe heute</p>
              </div>
            </div>
          </div>
          
          <div class="bg-white rounded-lg shadow-sm p-6 border border-gray-200">
            <div class="flex items-center">
              <div class="p-3 rounded-full bg-yellow-100 text-yellow-600 mr-4">
                <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 8l7.89 4.26a2 2 0 002.22 0L21 8M5 19h14a2 2 0 002-2V7a2 2 0 00-2-2H5a2 2 0 00-2 2v10a2 2 0 002 2z"></path>
                </svg>
              </div>
              <div>
                <p class="text-2xl font-bold text-gray-900">{{ totalInquiries }}</p>
                <p class="text-gray-600">Anfragen</p>
              </div>
            </div>
          </div>
        </div>

        <!-- Tabs -->
        <div class="bg-white rounded-lg shadow-sm border border-gray-200 mb-8">
          <div class="border-b border-gray-200">
            <nav class="-mb-px flex space-x-8 px-6">
              <button 
                @click="activeTab = 'properties'" 
                :class="[
                  'py-4 px-1 border-b-2 font-medium text-sm transition-colors',
                  activeTab === 'properties' 
                    ? 'border-blue-500 text-blue-600' 
                    : 'border-transparent text-gray-500 hover:text-gray-700 hover:border-gray-300'
                ]"
              >
                <svg class="w-5 h-5 inline mr-2" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 21V5a2 2 0 00-2-2H7a2 2 0 00-2 2v16m14 0h2m-2 0h-5m-9 0H3m2 0h5M9 7h1m-1 4h1m4-4h1m-1 4h1m-5 10v-5a1 1 0 011-1h2a1 1 0 011 1v5m-4 0h4"></path>
                </svg>
                Immobilien ({{ properties.length }})
              </button>
              <button 
                @click="activeTab = 'companies'" 
                :class="[
                  'py-4 px-1 border-b-2 font-medium text-sm transition-colors',
                  activeTab === 'companies' 
                    ? 'border-blue-500 text-blue-600' 
                    : 'border-transparent text-gray-500 hover:text-gray-700 hover:border-gray-300'
                ]"
              >
                <svg class="w-5 h-5 inline mr-2" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 21V5a2 2 0 00-2-2H7a2 2 0 00-2 2v16m14 0h2m-2 0h-5m-9 0H3m2 0h5M9 7h1m-1 4h1m4-4h1m-1 4h1m-5 10v-5a1 1 0 011-1h2a1 1 0 011 1v5m-4 0h4"></path>
                </svg>
                Unternehmen ({{ companies.length }})
              </button>
            </nav>
          </div>

          <!-- Properties Tab -->
          <div v-if="activeTab === 'properties'" class="p-6">
            <div class="grid grid-cols-1 xl:grid-cols-2 gap-8">
              <!-- Add Property Form -->
              <div class="bg-gray-50 rounded-lg p-6">
                <h2 class="text-xl font-semibold text-gray-900 mb-6 flex items-center">
                  <svg class="w-6 h-6 mr-2 text-blue-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 6v6m0 0v6m0-6h6m-6 0H6"></path>
                  </svg>
                  Neue Immobilie hinzufügen
                </h2>
                
                <form @submit.prevent="addProperty" class="space-y-4">
                  <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
                    <div>
                      <label class="block text-sm font-medium text-gray-700 mb-2">Titel *</label>
                      <input 
                        v-model="newProperty.title" 
                        type="text" 
                        required 
                        class="w-full px-3 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                        placeholder="z.B. Moderne Eigentumswohnung"
                      />
                    </div>
                    
                    <div>
                      <label class="block text-sm font-medium text-gray-700 mb-2">Typ *</label>
                      <select 
                        v-model="newProperty.type" 
                        required 
                        class="w-full px-3 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                      >
                        <option value="">Typ wählen</option>
                        <option value="Eigentumswohnung">Eigentumswohnung</option>
                        <option value="Einfamilienhaus">Einfamilienhaus</option>
                        <option value="Mehrfamilienhaus">Mehrfamilienhaus</option>
                        <option value="Penthouse">Penthouse</option>
                        <option value="Gewerbeimmobilie">Gewerbeimmobilie</option>
                        <option value="Grundstück">Grundstück</option>
                      </select>
                    </div>
                  </div>
                  
                  <div class="grid grid-cols-1 sm:grid-cols-3 gap-4">
                    <div>
                      <label class="block text-sm font-medium text-gray-700 mb-2">Preis (€) *</label>
                      <input 
                        v-model="newProperty.price" 
                        type="text" 
                        required 
                        class="w-full px-3 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                        placeholder="850.000"
                      />
                    </div>
                    
                    <div>
                      <label class="block text-sm font-medium text-gray-700 mb-2">Größe (m²) *</label>
                      <input 
                        v-model="newProperty.size" 
                        type="text" 
                        required 
                        class="w-full px-3 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                        placeholder="95"
                      />
                    </div>
                    
                    <div>
                      <label class="block text-sm font-medium text-gray-700 mb-2">Zimmer *</label>
                      <input 
                        v-model="newProperty.rooms" 
                        type="text" 
                        required 
                        class="w-full px-3 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                        placeholder="3.5"
                      />
                    </div>
                  </div>
                  
                  <div>
                    <label class="block text-sm font-medium text-gray-700 mb-2">Standort *</label>
                    <input 
                      v-model="newProperty.location" 
                      type="text" 
                      required 
                      class="w-full px-3 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                      placeholder="München-Schwabing"
                    />
                  </div>
                  
                  <div>
                    <label class="block text-sm font-medium text-gray-700 mb-2">Beschreibung *</label>
                    <textarea 
                      v-model="newProperty.description" 
                      required 
                      rows="3"
                      class="w-full px-3 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                      placeholder="Beschreiben Sie die Immobilie..."
                    ></textarea>
                  </div>
                  
                  <div>
                    <MultiImageUpload
                      label="Immobilien-Bilder"
                      v-model="newProperty.images"
                    />
                  </div>
                  
                  <button 
                    type="submit" 
                    :disabled="loading"
                    class="w-full bg-blue-600 text-white px-4 py-3 rounded-lg hover:bg-blue-700 disabled:opacity-50 disabled:cursor-not-allowed transition-colors font-medium"
                  >
                    <span v-if="loading" class="flex items-center justify-center">
                      <svg class="animate-spin -ml-1 mr-3 h-5 w-5 text-white" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
                        <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                        <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
                      </svg>
                      Wird hinzugefügt...
                    </span>
                    <span v-else>Immobilie hinzufügen</span>
                  </button>
                </form>
              </div>

              <!-- Properties List -->
              <div class="bg-gray-50 rounded-lg p-6">
                <h3 class="text-xl font-semibold text-gray-900 mb-6 flex items-center">
                  <svg class="w-6 h-6 mr-2 text-green-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5H7a2 2 0 00-2 2v10a2 2 0 002 2h8a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2"></path>
                  </svg>
                  Vorhandene Immobilien
                </h3>
                
                <div v-if="loading && properties.length === 0" class="text-center py-8">
                  <div class="inline-block animate-spin rounded-full h-8 w-8 border-b-2 border-blue-600"></div>
                  <p class="mt-2 text-gray-600">Lade Daten...</p>
                </div>
                
                <div v-else-if="error" class="text-center py-8">
                  <div class="text-red-600 mb-4">
                    <svg class="w-12 h-12 mx-auto mb-2" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-2.5L13.732 4c-.77-.833-1.964-.833-2.732 0L3.732 16.5c-.77.833.192 2.5 1.732 2.5z"></path>
                    </svg>
                  </div>
                  <p class="text-gray-600 mb-4">{{ error }}</p>
                  <button 
                    @click="loadProperties" 
                    class="px-4 py-2 bg-blue-600 text-white rounded-lg hover:bg-blue-700 transition-colors"
                  >
                    Erneut versuchen
                  </button>
                </div>
                
                <div v-else-if="properties.length === 0" class="text-center py-8">
                  <svg class="w-16 h-16 mx-auto text-gray-400 mb-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 21V5a2 2 0 00-2-2H7a2 2 0 00-2 2v16m14 0h2m-2 0h-5m-9 0H3m2 0h5M9 7h1m-1 4h1m4-4h1m-1 4h1m-5 10v-5a1 1 0 011-1h2a1 1 0 011 1v5m-4 0h4"></path>
                  </svg>
                  <h3 class="text-lg font-medium text-gray-900 mb-2">Keine Immobilien vorhanden</h3>
                  <p class="text-gray-600">Fügen Sie Ihre erste Immobilie hinzu.</p>
                </div>
                
                <div v-else class="space-y-4 max-h-96 overflow-y-auto">
                  <div 
                    v-for="property in properties" 
                    :key="property.id" 
                    class="bg-white rounded-lg p-4 shadow-sm border border-gray-200 hover:shadow-md transition-shadow"
                  >
                    <div class="flex items-start space-x-4">
                      <img 
                        :src="property.images && property.images.length > 0 ? property.images[0] : '/placeholder-image.jpg'" 
                        :alt="property.title" 
                        class="w-20 h-20 object-cover rounded-lg flex-shrink-0" 
                      />
                      <div class="flex-1 min-w-0">
                        <h4 class="text-lg font-semibold text-gray-900 truncate">{{ property.title }}</h4>
                        <p class="text-blue-600 font-medium">€{{ property.price }}</p>
                        <p class="text-gray-600 text-sm">{{ property.location }}</p>
                        <p class="text-gray-500 text-sm">{{ property.type }} • {{ property.size }}m² • {{ property.rooms }} Zimmer</p>
                      </div>
                      <button 
                        @click="deleteProperty(property.id)" 
                        class="bg-red-600 text-white p-2 rounded-lg hover:bg-red-700 transition-colors flex-shrink-0"
                        title="Löschen"
                      >
                        <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16"></path>
                        </svg>
                      </button>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>

          <!-- Companies Tab -->
          <div v-if="activeTab === 'companies'" class="p-6">
            <div class="grid grid-cols-1 xl:grid-cols-2 gap-8">
              <!-- Add Company Form -->
              <div class="bg-gray-50 rounded-lg p-6">
                <h2 class="text-xl font-semibold text-gray-900 mb-6 flex items-center">
                  <svg class="w-6 h-6 mr-2 text-green-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 6v6m0 0v6m0-6h6m-6 0H6"></path>
                  </svg>
                  Neues Unternehmen hinzufügen
                </h2>
                
                <form @submit.prevent="addCompany" class="space-y-4">
                  <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
                    <div>
                      <label class="block text-sm font-medium text-gray-700 mb-2">Name *</label>
                      <input 
                        v-model="newCompany.name" 
                        type="text" 
                        required 
                        class="w-full px-3 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                        placeholder="z.B. TechStart GmbH"
                      />
                    </div>
                    
                    <div>
                      <label class="block text-sm font-medium text-gray-700 mb-2">Branche *</label>
                      <input 
                        v-model="newCompany.industry" 
                        type="text" 
                        required 
                        class="w-full px-3 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                        placeholder="z.B. Software Development"
                      />
                    </div>
                  </div>
                  
                  <div class="grid grid-cols-1 sm:grid-cols-3 gap-4">
                    <div>
                      <label class="block text-sm font-medium text-gray-700 mb-2">Mitarbeiter</label>
                      <input 
                        v-model="newCompany.employees" 
                        type="text" 
                        class="w-full px-3 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                        placeholder="25-50"
                      />
                    </div>
                    
                    <div>
                      <label class="block text-sm font-medium text-gray-700 mb-2">Gegründet</label>
                      <input 
                        v-model="newCompany.founded" 
                        type="text" 
                        class="w-full px-3 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                        placeholder="2019"
                      />
                    </div>
                    
                    <div>
                      <label class="block text-sm font-medium text-gray-700 mb-2">Umsatz</label>
                      <input 
                        v-model="newCompany.revenue" 
                        type="text" 
                        class="w-full px-3 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                        placeholder="2.5M"
                      />
                    </div>
                  </div>
                  
                  <div>
                    <label class="block text-sm font-medium text-gray-700 mb-2">Standort *</label>
                    <input 
                      v-model="newCompany.location" 
                      type="text" 
                      required 
                      class="w-full px-3 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                      placeholder="München-Maxvorstadt"
                    />
                  </div>
                  
                  <div>
                    <label class="block text-sm font-medium text-gray-700 mb-2">Beschreibung *</label>
                    <textarea 
                      v-model="newCompany.description" 
                      required 
                      rows="3"
                      class="w-full px-3 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                      placeholder="Beschreiben Sie das Unternehmen..."
                    ></textarea>
                  </div>
                  
                  <div>
                    <MultiImageUpload
                      label="Firmen-Bilder"
                      v-model="newCompany.images"
                    />
                  </div>
                  
                  <button 
                    type="submit" 
                    :disabled="loading"
                    class="w-full bg-green-600 text-white px-4 py-3 rounded-lg hover:bg-green-700 disabled:opacity-50 disabled:cursor-not-allowed transition-colors font-medium"
                  >
                    <span v-if="loading" class="flex items-center justify-center">
                      <svg class="animate-spin -ml-1 mr-3 h-5 w-5 text-white" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
                        <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                        <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
                      </svg>
                      Wird hinzugefügt...
                    </span>
                    <span v-else>Unternehmen hinzufügen</span>
                  </button>
                </form>
              </div>

              <!-- Companies List -->
              <div class="bg-gray-50 rounded-lg p-6">
                <h3 class="text-xl font-semibold text-gray-900 mb-6 flex items-center">
                  <svg class="w-6 h-6 mr-2 text-blue-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5H7a2 2 0 00-2 2v10a2 2 0 002 2h8a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2"></path>
                  </svg>
                  Vorhandene Unternehmen
                </h3>
                
                <div v-if="loading && companies.length === 0" class="text-center py-8">
                  <div class="inline-block animate-spin rounded-full h-8 w-8 border-b-2 border-green-600"></div>
                  <p class="mt-2 text-gray-600">Lade Daten...</p>
                </div>
                
                <div v-else-if="error" class="text-center py-8">
                  <div class="text-red-600 mb-4">
                    <svg class="w-12 h-12 mx-auto mb-2" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-2.5L13.732 4c-.77-.833-1.964-.833-2.732 0L3.732 16.5c-.77.833.192 2.5 1.732 2.5z"></path>
                    </svg>
                  </div>
                  <p class="text-gray-600 mb-4">{{ error }}</p>
                  <button 
                    @click="loadCompanies" 
                    class="px-4 py-2 bg-green-600 text-white rounded-lg hover:bg-green-700 transition-colors"
                  >
                    Erneut versuchen
                  </button>
                </div>
                
                <div v-else-if="companies.length === 0" class="text-center py-8">
                  <svg class="w-16 h-16 mx-auto text-gray-400 mb-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 21V5a2 2 0 00-2-2H7a2 2 0 00-2 2v16m14 0h2m-2 0h-5m-9 0H3m2 0h5M9 7h1m-1 4h1m4-4h1m-1 4h1m-5 10v-5a1 1 0 011-1h2a1 1 0 011 1v5m-4 0h4"></path>
                  </svg>
                  <h3 class="text-lg font-medium text-gray-900 mb-2">Keine Unternehmen vorhanden</h3>
                  <p class="text-gray-600">Fügen Sie Ihr erstes Unternehmen hinzu.</p>
                </div>
                
                <div v-else class="space-y-4 max-h-96 overflow-y-auto">
                  <div 
                    v-for="company in companies" 
                    :key="company.id" 
                    class="bg-white rounded-lg p-4 shadow-sm border border-gray-200 hover:shadow-md transition-shadow"
                  >
                    <div class="flex items-start space-x-4">
                      <img 
                        :src="company.images && company.images.length > 0 ? company.images[0] : '/placeholder-image.jpg'" 
                        :alt="company.name" 
                        class="w-20 h-20 object-cover rounded-lg flex-shrink-0" 
                      />
                      <div class="flex-1 min-w-0">
                        <h4 class="text-lg font-semibold text-gray-900 truncate">{{ company.name }}</h4>
                        <p class="text-green-600 font-medium">{{ company.industry }}</p>
                        <p class="text-gray-600 text-sm">{{ company.location }}</p>
                        <p class="text-gray-500 text-sm">{{ company.employees }} Mitarbeiter • Gegründet {{ company.founded }}</p>
                      </div>
                      <button 
                        @click="deleteCompany(company.id)" 
                        class="bg-red-600 text-white p-2 rounded-lg hover:bg-red-700 transition-colors flex-shrink-0"
                        title="Löschen"
                      >
                        <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16"></path>
                        </svg>
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
import { ref, onMounted, computed } from 'vue'
import { propertiesAPI, companiesAPI } from '../services/api'
import { authService } from '../services/auth'
import MultiImageUpload from '../components/MultiImageUpload.vue'

const isAuthenticated = ref(false)
const activeTab = ref('properties')
const loading = ref(false)
const error = ref('')

// Mock data for demo
const totalViews = ref(1247)
const totalInquiries = ref(23)

// Login form
const loginForm = ref({
  username: '',
  password: ''
})
const loginLoading = ref(false)
const loginError = ref('')

// Properties data
const properties = ref([])
const newProperty = ref({
  title: '',
  price: '',
  location: '',
  type: '',
  size: '',
  rooms: '',
  description: '',
  images: [],
  features: [],
  contact: {
    email: 'info@immobilienmakler.de',
    phone: '+49 89 123 456'
  },
  details: {}
})

// Companies data
const companies = ref([])
const newCompany = ref({
  name: '',
  industry: '',
  location: '',
  description: '',
  images: [],
  employees: '',
  founded: '',
  revenue: '',
  highlights: [],
  contact: {
    email: 'info@immobilienmakler.de',
    phone: '+49 89 123 456'
  }
})

// Check authentication on mount
onMounted(async () => {
  const isValid = await authService.verifyToken()
  if (isValid) {
    isAuthenticated.value = true
    await loadProperties()
    await loadCompanies()
  }
})

// Authentication functions
async function handleLogin() {
  try {
    loginLoading.value = true
    loginError.value = ''
    
    await authService.login({
      username: loginForm.value.username,
      password: loginForm.value.password
    })
    
    isAuthenticated.value = true
    await loadProperties()
    await loadCompanies()
  } catch (err) {
    loginError.value = err instanceof Error ? err.message : 'Login fehlgeschlagen'
  } finally {
    loginLoading.value = false
  }
}

function handleLogout() {
  authService.logout()
  isAuthenticated.value = false
  properties.value = []
  companies.value = []
  loginForm.value = { username: '', password: '' }
}

// Properties functions
async function loadProperties() {
  try {
    loading.value = true
    error.value = ''
    const response = await propertiesAPI.getAll()
    properties.value = response
  } catch (err) {
    error.value = 'Fehler beim Laden der Immobilien'
    console.error('Error loading properties:', err)
  } finally {
    loading.value = false
  }
}

async function addProperty() {
  try {
    loading.value = true
    error.value = ''
    await propertiesAPI.create(newProperty.value)
    await loadProperties()
    
    // Reset form
    newProperty.value = {
      title: '',
      price: '',
      location: '',
      type: '',
      size: '',
      rooms: '',
      description: '',
      images: [],
      features: [],
      contact: {
        email: 'info@immobilienmakler.de',
        phone: '+49 89 123 456'
      },
      details: {}
    }
    
    // Show success message
    alert('Immobilie erfolgreich hinzugefügt!')
  } catch (err) {
    error.value = 'Fehler beim Hinzufügen der Immobilie'
    console.error('Error adding property:', err)
    alert('Fehler beim Hinzufügen der Immobilie')
  } finally {
    loading.value = false
  }
}

async function deleteProperty(id: number) {
  if (!confirm('Sind Sie sicher, dass Sie diese Immobilie löschen möchten?')) {
    return
  }
  
  try {
    loading.value = true
    error.value = ''
    await propertiesAPI.delete(id)
    await loadProperties()
    alert('Immobilie erfolgreich gelöscht!')
  } catch (err) {
    error.value = 'Fehler beim Löschen der Immobilie'
    console.error('Error deleting property:', err)
    alert('Fehler beim Löschen der Immobilie')
  } finally {
    loading.value = false
  }
}

// Companies functions
async function loadCompanies() {
  try {
    loading.value = true
    error.value = ''
    const response = await companiesAPI.getAll()
    companies.value = response
  } catch (err) {
    error.value = 'Fehler beim Laden der Unternehmen'
    console.error('Error loading companies:', err)
  } finally {
    loading.value = false
  }
}

async function addCompany() {
  try {
    loading.value = true
    error.value = ''
    await companiesAPI.create(newCompany.value)
    await loadCompanies()
    
    // Reset form
    newCompany.value = {
      name: '',
      industry: '',
      location: '',
      description: '',
      images: [],
      employees: '',
      founded: '',
      revenue: '',
      highlights: [],
      contact: {
        email: 'info@immobilienmakler.de',
        phone: '+49 89 123 456'
      }
    }
    
    // Show success message
    alert('Unternehmen erfolgreich hinzugefügt!')
  } catch (err) {
    error.value = 'Fehler beim Hinzufügen des Unternehmens'
    console.error('Error adding company:', err)
    alert('Fehler beim Hinzufügen des Unternehmens')
  } finally {
    loading.value = false
  }
}

async function deleteCompany(id: number) {
  if (!confirm('Sind Sie sicher, dass Sie dieses Unternehmen löschen möchten?')) {
    return
  }
  
  try {
    loading.value = true
    error.value = ''
    await companiesAPI.delete(id)
    await loadCompanies()
    alert('Unternehmen erfolgreich gelöscht!')
  } catch (err) {
    error.value = 'Fehler beim Löschen des Unternehmens'
    console.error('Error deleting company:', err)
    alert('Fehler beim Löschen des Unternehmens')
  } finally {
    loading.value = false
  }
}
</script>

<style scoped>
.admin-container {
  min-height: 100vh;
}
</style>