
# 📍 Geolocation Finder by Aruthra

A beautiful and interactive **geotagging web app** that helps users find and copy the **latitude and longitude** of any place using either a **search box or their current location**. The app displays results on an interactive map powered by **Leaflet** and styled with **Tailwind CSS**.


## 🌟 Features

- 🔍 Search any location by address, landmark, or city
- 📍 Get your current device location (if supported)
- 🗺️ View coordinates visually on a dynamic Leaflet map
- 📋 Copy coordinates to clipboard with one click
- 📱 Fully responsive and mobile-friendly UI



## 📸 Live Demo

> [🔗 GitHub Pages Link (https://aruthra07.github.io/geotag/)]  
> 


## 🛠️ Tech Stack

- **HTML5**
- **Tailwind CSS** for UI styling
- **Vanilla JavaScript** for logic
- **Leaflet.js** for maps
- **OpenStreetMap (Nominatim API)** for geocoding



## 📁 Project Structure

```

📦 geolocation-finder
┣ 📄 index.html
┣ 📄 README.md

````

---

## 🚀 How to Run

1. Clone the repo:
   ```bash
   git clone https://github.com/your-username/geolocation-finder.git
````

2. Navigate to the folder and open `index.html` in any modern browser:

   ```bash
   cd geolocation-finder
   open index.html
   ```

3. Allow location permission when prompted for full functionality.

---

## 🧠 How It Works

* The **search input** uses the Nominatim API from OpenStreetMap to convert addresses into coordinates (geocoding).
* The **"Use My Location"** button uses the browser's `navigator.geolocation` API to get your current location and optionally reverse-geocodes it to a human-readable address.
* The **Leaflet.js** map displays the location visually with a marker popup showing latitude and longitude.

---

## 📌 Use Cases

* Tagging photo locations manually
* Travel planning and sharing coordinates
* Educational demos for students learning geolocation
* Offline geotag-based project frontends

---

## 🙋‍♀️ Author

Built with ❤️ by **Aruthra**
Follow me on [GitHub](#) or [LinkedIn](#) for more!

---

## 📜 License

This project is open-source and available under the [MIT License](LICENSE).

---

## ✅ To-Do / Improvements (Optional)

* [ ] Add map themes (dark mode, satellite view)
* [ ] Enable export to CSV or JSON
* [ ] Add marker saving/bookmark feature

```

---

### ✅ What You Should Do Next:
- Save this content as `README.md` in your project folder.
- Add screenshots or GIFs of the app to enhance it visually.
- Replace placeholders like `GitHub Pages Link` and author profile links as needed.

Would you like me to create a **project banner** or help you with **hosting on GitHub Pages** too?
```
