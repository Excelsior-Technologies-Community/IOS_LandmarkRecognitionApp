
🏛️ LandmarkRecognitionApp (SwiftUI)

An iOS SwiftUI app that allows users to upload or capture an image of a landmark and automatically detect its name and geographical coordinates (latitude & longitude) using Google Cloud Vision API – Landmark Detection.

This project is 100% free to run under Google’s free tier (no paid subscription required).

⸻

📱 What This App Does

✅ Pick an image from Gallery or Camera
✅ Upload the image to Google Vision API
✅ Detect Landmark Name (e.g. Eiffel Tower)
✅ Extract Latitude & Longitude
✅ Display results clearly in the UI

⸻

🧠 How It Works (High Level Flow)

User selects image
        ↓
Image converted to Base64
        ↓
Google Vision API (LANDMARK_DETECTION)
        ↓
API Response (JSON)
        ↓
Parse:
- Landmark Name
- Latitude
- Longitude
        ↓
Show results in SwiftUI


⸻

🗂️ Project Structure

LandmarkRecognitionApp/
│
├── LandmarkRecognitionAppApp.swift
├── ContentView.swift
├── Assets.xcassets


⸻

🧩 File-by-File Explanation

⸻

📄 LandmarkRecognitionAppApp.swift

Purpose:
This is the entry point of the SwiftUI app.

Why we use it:
SwiftUI apps start here. It loads ContentView as the root screen.

import SwiftUI

@main
struct LandmarkRecognitionAppApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
    }
}


⸻

📄 ContentView.swift

Purpose:
This file contains:
    •    UI
    •    Image picker
    •    Google Vision API call
    •    JSON parsing
    •    Result display

⸻

🖼️ Image Selection Logic

Why:
User needs to upload an image for landmark detection.

@State private var selectedImage: UIImage?
@State private var showImagePicker = false

Button("Select Image") {
    showImagePicker = true
}


⸻

🔄 Image → Base64 Conversion

Why Google Vision needs Base64:
Google Vision API accepts images only in Base64 encoded string format.

func convertImageToBase64(_ image: UIImage) -> String? {
    guard let imageData = image.jpegData(compressionQuality: 0.8) else { return nil }
    return imageData.base64EncodedString()
}


⸻

🌍 Google Vision API – Landmark Detection

API Endpoint Used

https://vision.googleapis.com/v1/images:annotate


⸻

🔑 How to Get Google Vision API Key (FREE)
    1.    Go to 👉 https://console.cloud.google.com
    2.    Create a New Project
    3.    Enable Vision API
    4.    Go to APIs & Services → Credentials
    5.    Create API Key
    6.    Copy the key

⚠️ Free Tier Available
Google provides free monthly requests, perfect for learning & demo apps.

⸻

🔐 API Key Usage (IMPORTANT)

Replace this in your code:

let apiKey = "YOUR_API_KEY_HERE"

👉 Developers must use their own API key

⸻

📡 Sending Image to Vision API

func detectLandmark(image: UIImage) {
    guard let base64Image = convertImageToBase64(image) else { return }

    let requestBody: [String: Any] = [
        "requests": [
            [
                "image": ["content": base64Image],
                "features": [
                    ["type": "LANDMARK_DETECTION", "maxResults": 1]
                ]
            ]
        ]
    ]


⸻

📬 API Request Code

let url = URL(string:
"https://vision.googleapis.com/v1/images:annotate?key=\(apiKey)")!

var request = URLRequest(url: url)
request.httpMethod = "POST"
request.addValue("application/json", forHTTPHeaderField: "Content-Type")
request.httpBody = try? JSONSerialization.data(withJSONObject: requestBody)


⸻

📥 Handling API Response

Extracting Landmark Name

let landmarkName =
landmark["description"] as? String ?? "Unknown"


⸻

Extracting Latitude & Longitude

📌 Important:
Google Vision returns coordinates inside:

locations → latLng → latitude / longitude

if let locations = landmark["locations"] as? [[String: Any]],
   let latLng = locations.first?["latLng"] as? [String: Any] {

    let latitude = latLng["latitude"] as? Double ?? 0.0
    let longitude = latLng["longitude"] as? Double ?? 0.0
}


⸻

📍 How Location Data Is Derived
    •    Vision API internally uses Google Maps + ML
    •    It identifies the landmark visually
    •    Maps it to known coordinates
    •    Returns precise latitude & longitude

✔️ No GPS
✔️ No user location permission required

⸻

🖥️ UI Display Logic

Text("Landmark: \(landmarkName)")
Text("Latitude: \(latitude)")
Text("Longitude: \(longitude)")


⸻

💡 Why Google Vision API?

✅ High accuracy
✅ Landmark recognition built-in
✅ Free tier available
✅ No ML training required

⸻

🚀 How to Run This Project
    1.    Clone the repo
    2.    Open .xcodeproj
    3.    Add your Google Vision API key
    4.    Run on iOS 16+
    5.    Upload a landmark image
    6.    See results 🎉

⸻

🔒 Privacy & Permissions
    •    No user location used
    •    No background tracking
    •    Image sent only to Google Vision API

⸻

🧪 Example Use Cases
    •    Travel apps
    •    Education apps
    •    Tourism guides
    •    Image recognition demos
    •    ML learning projects

⸻

📌 Future Improvements
    •    Show landmark on map
    •    Multiple landmark detection
    •    Offline cache
    •    History screen

⸻
