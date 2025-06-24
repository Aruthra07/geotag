# geotag<!DOCTYPE html>
<html lang="en">
<head>
    <footer>
        Created by Aruthra
    </footer>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Geolocation Finder</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <style>
        #map {
            height: 400px;
            width: 100%;
            border-radius: 0.5rem;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
        }
        .loading-spinner {
            display: none;
            border: 3px solid rgba(0, 0, 0, 0.1);
            border-radius: 50%;
            border-top: 3px solid #3b82f6;
            width: 20px;
            height: 20px;
            animation: spin 1s linear infinite;
            margin-left: 10px;
        }
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
    </style>
</head>
<body class="bg-gray-100 min-h-screen flex flex-col items-center py-10 px-4">
    <div class="w-full max-w-3xl bg-white rounded-xl shadow-lg p-6 space-y-6">
        <div class="text-center">
            <h1 class="text-3xl font-bold text-gray-800">Geolocation Finder</h1>
            <p class="text-gray-600 mt-2">Find latitude and longitude coordinates for any location</p>
        </div>

        <div class="flex flex-col md:flex-row gap-4">
            <div class="flex-1">
                <div class="relative">
                    <input type="text" id="locationInput" placeholder="Enter an address, city, or landmark" 
                           class="w-full px-4 py-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500 outline-none transition">
                    <button id="searchBtn" class="absolute right-2 top-1/2 transform -translate-y-1/2 bg-blue-500 text-white px-4 py-1 rounded-lg hover:bg-blue-600 transition flex items-center">
                        Search
                        <div id="searchSpinner" class="loading-spinner"></div>
                    </button>
                </div>
                <p class="text-sm text-gray-500 mt-1">Example: "Eiffel Tower" or "1600 Pennsylvania Ave"</p>
            </div>
            <button id="currentLocationBtn" class="bg-green-500 hover:bg-green-600 text-white px-4 py-3 rounded-lg transition whitespace-nowrap flex items-center justify-center">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-2" viewBox="0 0 20 20" fill="currentColor">
                    <path fill-rule="evenodd" d="M5.05 4.05a7 7 0 119.9 9.9L10 18.9l-4.95-4.95a7 7 0 010-9.9zM10 11a2 2 0 100-4 2 2 0 000 4z" clip-rule="evenodd" />
                </svg>
                Use My Location
                <div id="currentLocSpinner" class="loading-spinner"></div>
            </button>
        </div>

        <div id="results" class="hidden bg-gray-50 p-4 rounded-lg">
            <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                <div>
                    <h3 class="font-semibold text-gray-700">Location Information</h3>
                    <div class="mt-2 space-y-2">
                        <p class="text-sm"><span class="font-medium">Address:</span> <span id="address" class="text-gray-600">-</span></p>
                        <p class="text-sm"><span class="font-medium">Latitude:</span> <span id="latitude" class="text-gray-600">-</span></p>
                        <p class="text-sm"><span class="font-medium">Longitude:</span> <span id="longitude" class="text-gray-600">-</span></p>
                    </div>
                </div>
                <div>
                    <h3 class="font-semibold text-gray-700">Coordinates</h3>
                    <div class="mt-2">
                        <div class="flex items-center space-x-2">
                            <input type="text" id="coordinatesOutput" readonly class="flex-1 px-3 py-2 border border-gray-300 rounded-lg bg-gray-100 text-gray-700">
                            <button id="copyBtn" class="bg-blue-500 hover:bg-blue-600 text-white px-3 py-2 rounded-lg transition">
                                Copy
                            </button>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <div id="map"></div>

        <div class="bg-blue-50 p-4 rounded-lg">
            <h3 class="font-semibold text-blue-800">How to use:</h3>
            <ol class="list-decimal list-inside text-sm text-blue-700 mt-2 space-y-1">
                <li>Enter a location in the search box or click "Use My Location"</li>
                <li>The coordinates will appear along with a map</li>
                <li>Click "Copy" to copy the coordinates to your clipboard</li>
            </ol>
        </div>
    </div>
    <footer>
        Created by Aruthra
    </footer>
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    <script>
        document.addEventListener('DOMContentLoaded', function() {
            // Initialize map
            const map = L.map('map').setView([0, 0], 2);
            L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
                attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
            }).addTo(map);
            
            let marker = null;
            const searchBtn = document.getElementById('searchBtn');
            const currentLocationBtn = document.getElementById('currentLocationBtn');
            const searchSpinner = document.getElementById('searchSpinner');
            const currentLocSpinner = document.getElementById('currentLocSpinner');
            const resultsDiv = document.getElementById('results');
            const coordinatesOutput = document.getElementById('coordinatesOutput');
            const copyBtn = document.getElementById('copyBtn');
            
            // Search for location
            searchBtn.addEventListener('click', searchLocation);
            document.getElementById('locationInput').addEventListener('keypress', function(e) {
                if (e.key === 'Enter') searchLocation();
            });
            
            // Get current location
            currentLocationBtn.addEventListener('click', getCurrentLocation);
            
            // Copy coordinates
            copyBtn.addEventListener('click', function() {
                coordinatesOutput.select();
                document.execCommand('copy');
                const originalText = copyBtn.textContent;
                copyBtn.textContent = 'Copied!';
                setTimeout(() => {
                    copyBtn.textContent = originalText;
                }, 2000);
            });
            
            function searchLocation() {
                const location = document.getElementById('locationInput').value.trim();
                if (!location) {
                    alert('Please enter a location');
                    return;
                }
                
                searchSpinner.style.display = 'block';
                searchBtn.disabled = true;
                
                // Use Nominatim API for geocoding
                fetch(`https://nominatim.openstreetmap.org/search?format=json&q=${encodeURIComponent(location)}`)
                    .then(response => response.json())
                    .then(data => {
                        searchSpinner.style.display = 'none';
                        searchBtn.disabled = false;
                        
                        if (data.length === 0) {
                            alert('Location not found. Please try a different search term.');
                            return;
                        }
                        
                        const firstResult = data[0];
                        const lat = parseFloat(firstResult.lat);
                        const lon = parseFloat(firstResult.lon);
                        
                        displayResults(firstResult.display_name, lat, lon);
                        updateMap(lat, lon);
                    })
                    .catch(error => {
                        searchSpinner.style.display = 'none';
                        searchBtn.disabled = false;
                        console.error('Error:', error);
                        alert('An error occurred while searching. Please try again.');
                    });
            }
            
            function getCurrentLocation() {
                if (!navigator.geolocation) {
                    alert('Geolocation is not supported by your browser');
                    return;
                }
                
                currentLocSpinner.style.display = 'block';
                currentLocationBtn.disabled = true;
                
                navigator.geolocation.getCurrentPosition(
                    position => {
                        const lat = position.coords.latitude;
                        const lon = position.coords.longitude;
                        
                        // Reverse geocoding to get address
                        fetch(`https://nominatim.openstreetmap.org/reverse?format=json&lat=${lat}&lon=${lon}`)
                            .then(response => response.json())
                            .then(data => {
                                currentLocSpinner.style.display = 'none';
                                currentLocationBtn.disabled = false;
                                
                                const address = data.display_name || 'Your current location';
                                displayResults(address, lat, lon);
                                updateMap(lat, lon);
                            })
                            .catch(error => {
                                currentLocSpinner.style.display = 'none';
                                currentLocationBtn.disabled = false;
                                console.error('Error:', error);
                                alert('Could not get address information, but coordinates are available.');
                                displayResults('Your current location', lat, lon);
                                updateMap(lat, lon);
                            });
                    },
                    error => {
                        currentLocSpinner.style.display = 'none';
                        currentLocationBtn.disabled = false;
                        alert('Error getting your location: ' + error.message);
                    }
                );
            }
            
            function displayResults(address, lat, lon) {
                document.getElementById('address').textContent = address;
                document.getElementById('latitude').textContent = lat.toFixed(6);
                document.getElementById('longitude').textContent = lon.toFixed(6);
                coordinatesOutput.value = `${lat.toFixed(6)}, ${lon.toFixed(6)}`;
                resultsDiv.classList.remove('hidden');
            }
            
            function updateMap(lat, lon) {
                map.setView([lat, lon], 15);
                
                if (marker) {
                    map.removeLayer(marker);
                }
                
                marker = L.marker([lat, lon]).addTo(map)
                    .bindPopup(`<b>Coordinates:</b><br>${lat.toFixed(6)}, ${lon.toFixed(6)}`)
                    .openPopup();
            }
        });
    </script>
</body>
</html>
