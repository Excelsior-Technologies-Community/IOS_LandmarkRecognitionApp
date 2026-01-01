# 🏛️ LandmarkRecognitionApp

## AI-Powered Landmark Detection using SwiftUI & Google Vision API

An elegant iOS SwiftUI application that leverages Google Cloud Vision API to automatically identify landmarks from images and extract their geographical coordinates. Perfect for travel apps, educational tools, and portfolio projects.

[![Swift](https://img.shields.io/badge/Swift-5.9-orange.svg)](https://swift.org)
[![iOS](https://img.shields.io/badge/iOS-16.0+-blue.svg)](https://developer.apple.com/ios/)
[![SwiftUI](https://img.shields.io/badge/SwiftUI-4.0-green.svg)](https://developer.apple.com/xcode/swiftui/)
[![License](https://img.shields.io/badge/License-MIT-purple.svg)](LICENSE)

> 🎯 **100% FREE** to run under Google's free tier (1000 requests/month)

---

## ✨ Features

- 📸 **Image Selection** - Pick from gallery or capture with camera
- 🏛️ **Landmark Detection** - Automatically identifies famous landmarks
- 🌍 **GPS Coordinates** - Extracts precise latitude & longitude
- 🎨 **Modern UI** - Clean SwiftUI interface
- ⚡ **Real-time Processing** - Fast API integration
- 🔒 **Privacy-First** - No user location tracking
- 💰 **Zero Cost** - Completely free under Google's tier

---

## 🎬 Demo

### App Flow

```
User selects/captures image
        ↓
Image converted to Base64
        ↓
Sent to Google Vision API
        ↓
LANDMARK_DETECTION feature
        ↓
Parse JSON response:
  • Landmark Name
  • Latitude
  • Longitude
        ↓
Display results in UI
```

### Example Output

```
🏛️ Landmark: Eiffel Tower
📍 Latitude: 48.8584
📍 Longitude: 2.2945
```

---

## 🏗️ Project Structure

```
LandmarkRecognitionApp/
│
├── App/
│   └── LandmarkRecognitionAppApp.swift    # App entry point
│
├── Views/
│   └── ContentView.swift                   # Main UI & API logic
│
├── Resources/
│   └── Assets.xcassets                     # Images & colors
│
└── Info.plist                              # App configuration
```

**Architecture**: Single-view SwiftUI app with inline API handling (perfect for learning and prototyping)

---

## 🔑 API Setup Guide

### Prerequisites

- Google Cloud Account (free)
- Valid payment method for verification (no charges for free tier)

### Step-by-Step Setup

#### 1️⃣ Create Google Cloud Project

1. Visit [Google Cloud Console](https://console.cloud.google.com)
2. Click **Select a project** → **New Project**
3. Name it: `LandmarkRecognitionApp`
4. Click **Create**

#### 2️⃣ Enable Vision API

1. Navigate to **APIs & Services** → **Library**
2. Search for **Cloud Vision API**
3. Click on it and press **Enable**

#### 3️⃣ Create API Key

1. Go to **APIs & Services** → **Credentials**
2. Click **+ Create Credentials** → **API Key**
3. Copy the generated key
4. (Optional) Click **Restrict Key** to limit usage to Vision API only

#### 4️⃣ Set Up Billing (Required for Free Tier)

> ⚠️ **Important**: Google requires billing info but **won't charge** under free limits

1. Go to **Billing** in Cloud Console
2. Add a payment method
3. Set up budget alerts at $0 to monitor usage
4. Free tier includes **1000 requests/month**

---

## 🔐 Configure API Key

Open `ContentView.swift` and replace the placeholder:

```swift
// ⚠️ Replace with your actual API key
private let apiKey = "YOUR_GOOGLE_VISION_API_KEY_HERE"
```

> 🔒 **Security Note**: Never commit real API keys to public repositories. Consider using:
> - Environment variables
> - `.xcconfig` files (gitignored)
> - Keychain storage for production

---

## 💻 Complete Code Implementation

### 📄 `LandmarkRecognitionAppApp.swift` - App Entry Point

**Purpose**: SwiftUI app entry point that loads the main view.

```swift
import SwiftUI

@main
struct LandmarkRecognitionAppApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
    }
}
```

**Why this code?**
- `@main` attribute marks the app's entry point
- `WindowGroup` creates the root scene for iOS apps
- `ContentView()` is instantiated as the initial view

---

### 📄 `ContentView.swift` - Main View & Logic

**Purpose**: Contains UI, image picker, API integration, and result display.

#### State Management

```swift
import SwiftUI

struct ContentView: View {
    // API Configuration
    private let apiKey = "YOUR_GOOGLE_VISION_API_KEY_HERE"
    
    // State Variables
    @State private var selectedImage: UIImage?
    @State private var showImagePicker = false
    @State private var isLoading = false
    
    // Results
    @State private var landmarkName: String = ""
    @State private var latitude: Double = 0.0
    @State private var longitude: Double = 0.0
    @State private var errorMessage: String?
```

**Why this code?**
- `@State` creates reactive UI bindings
- `private` encapsulates internal state
- Optional `selectedImage` handles no-selection state
- Separate loading and error states for better UX

---

#### Image Picker Integration

```swift
    var body: some View {
        VStack(spacing: 20) {
            // Image Display
            if let image = selectedImage {
                Image(uiImage: image)
                    .resizable()
                    .scaledToFit()
                    .frame(height: 250)
                    .cornerRadius(12)
                    .shadow(radius: 5)
            } else {
                RoundedRectangle(cornerRadius: 12)
                    .fill(Color.gray.opacity(0.3))
                    .frame(height: 250)
                    .overlay(
                        Text("No Image Selected")
                            .foregroundColor(.gray)
                    )
            }
            
            // Select Image Button
            Button(action: { showImagePicker = true }) {
                Label("Select Landmark Image", systemImage: "photo.on.rectangle")
                    .font(.headline)
                    .foregroundColor(.white)
                    .padding()
                    .frame(maxWidth: .infinity)
                    .background(Color.blue)
                    .cornerRadius(10)
            }
            
            // Detect Button
            if selectedImage != nil {
                Button(action: { detectLandmark() }) {
                    Label("Detect Landmark", systemImage: "location.magnifyingglass")
                        .font(.headline)
                        .foregroundColor(.white)
                        .padding()
                        .frame(maxWidth: .infinity)
                        .background(Color.green)
                        .cornerRadius(10)
                }
            }
        }
        .sheet(isPresented: $showImagePicker) {
            ImagePicker(selectedImage: $selectedImage)
        }
    }
```

**Why this code?**
- Conditional rendering with `if let` for optional image
- `Label` combines icon + text elegantly
- `sheet` presents image picker modally
- Separate buttons for selection and detection (better UX)

---

#### Base64 Conversion

```swift
    // MARK: - Image to Base64 Conversion
    
    private func convertImageToBase64(_ image: UIImage) -> String? {
        guard let imageData = image.jpegData(compressionQuality: 0.8) else {
            return nil
        }
        return imageData.base64EncodedString()
    }
```

**Why this code?**
- **compressionQuality: 0.8**: Balances quality vs file size
  - Lower = smaller files, faster upload
  - 0.8 is optimal for API usage
- **Base64 encoding**: Required format for Google Vision API
- **guard statement**: Safe unwrapping prevents crashes

---

#### Google Vision API Integration

```swift
    // MARK: - Landmark Detection
    
    private func detectLandmark() {
        guard let image = selectedImage,
              let base64Image = convertImageToBase64(image) else {
            errorMessage = "Failed to process image"
            return
        }
        
        isLoading = true
        errorMessage = nil
        
        // Construct API request body
        let requestBody: [String: Any] = [
            "requests": [
                [
                    "image": [
                        "content": base64Image
                    ],
                    "features": [
                        [
                            "type": "LANDMARK_DETECTION",
                            "maxResults": 1
                        ]
                    ]
                ]
            ]
        ]
        
        // API endpoint
        let urlString = "https://vision.googleapis.com/v1/images:annotate?key=\(apiKey)"
        guard let url = URL(string: urlString) else {
            errorMessage = "Invalid API URL"
            isLoading = false
            return
        }
        
        // Configure request
        var request = URLRequest(url: url)
        request.httpMethod = "POST"
        request.addValue("application/json", forHTTPHeaderField: "Content-Type")
        
        do {
            request.httpBody = try JSONSerialization.data(withJSONObject: requestBody)
        } catch {
            errorMessage = "Failed to create request: \(error.localizedDescription)"
            isLoading = false
            return
        }
        
        // Send request
        URLSession.shared.dataTask(with: request) { data, response, error in
            DispatchQueue.main.async {
                isLoading = false
                
                if let error = error {
                    errorMessage = "Network error: \(error.localizedDescription)"
                    return
                }
                
                guard let data = data else {
                    errorMessage = "No data received"
                    return
                }
                
                parseResponse(data)
            }
        }.resume()
    }
```

**Why this code?**
- **Guard statements**: Early returns prevent nested if-else
- **Error handling**: User-friendly messages for each failure point
- **DispatchQueue.main.async**: UI updates must happen on main thread
- **URLSession**: Standard iOS networking
- **JSON body**: Matches Google's API specification exactly
- **maxResults: 1**: We only need the most confident match

---

#### Response Parsing

```swift
    // MARK: - Parse API Response
    
    private func parseResponse(_ data: Data) {
        do {
            // Parse JSON
            guard let json = try JSONSerialization.jsonObject(with: data) as? [String: Any],
                  let responses = json["responses"] as? [[String: Any]],
                  let firstResponse = responses.first else {
                errorMessage = "Invalid response format"
                return
            }
            
            // Check for API errors
            if let error = firstResponse["error"] as? [String: Any],
               let message = error["message"] as? String {
                errorMessage = "API Error: \(message)"
                return
            }
            
            // Extract landmark annotations
            guard let landmarks = firstResponse["landmarkAnnotations"] as? [[String: Any]],
                  let landmark = landmarks.first else {
                errorMessage = "No landmark detected in this image"
                return
            }
            
            // Extract landmark name
            landmarkName = landmark["description"] as? String ?? "Unknown Landmark"
            
            // Extract coordinates
            if let locations = landmark["locations"] as? [[String: Any]],
               let firstLocation = locations.first,
               let latLng = firstLocation["latLng"] as? [String: Any] {
                
                latitude = latLng["latitude"] as? Double ?? 0.0
                longitude = latLng["longitude"] as? Double ?? 0.0
            } else {
                errorMessage = "Coordinates not available"
            }
            
        } catch {
            errorMessage = "Failed to parse response: \(error.localizedDescription)"
        }
    }
}
```

**Why this code?**
- **Nested guard statements**: Safely unwrap deep JSON structures
- **Error propagation**: API errors are captured and displayed
- **landmarkAnnotations**: Google Vision's response format
- **locations → latLng**: Precise path to coordinates
- **Fallback values**: `??` operator provides defaults
- **Clear error messages**: Helps debugging API issues

---

#### ImagePicker Helper

```swift
// MARK: - Image Picker

struct ImagePicker: UIViewControllerRepresentable {
    @Binding var selectedImage: UIImage?
    @Environment(\.dismiss) var dismiss
    
    func makeUIViewController(context: Context) -> UIImagePickerController {
        let picker = UIImagePickerController()
        picker.delegate = context.coordinator
        picker.sourceType = .photoLibrary
        return picker
    }
    
    func updateUIViewController(_ uiViewController: UIImagePickerController, context: Context) {}
    
    func makeCoordinator() -> Coordinator {
        Coordinator(self)
    }
    
    class Coordinator: NSObject, UIImagePickerControllerDelegate, UINavigationControllerDelegate {
        let parent: ImagePicker
        
        init(_ parent: ImagePicker) {
            self.parent = parent
        }
        
        func imagePickerController(_ picker: UIImagePickerController,
                                   didFinishPickingMediaWithInfo info: [UIImagePickerController.InfoKey: Any]) {
            if let image = info[.originalImage] as? UIImage {
                parent.selectedImage = image
            }
            parent.dismiss()
        }
        
        func imagePickerControllerDidCancel(_ picker: UIImagePickerController) {
            parent.dismiss()
        }
    }
}
```

**Why this code?**
- **UIViewControllerRepresentable**: Bridges UIKit to SwiftUI
- **@Environment(\.dismiss)**: Modern way to dismiss sheets
- **Coordinator pattern**: Handles UIKit delegate callbacks
- **didFinishPickingMediaWithInfo**: Callback when image selected
- **.originalImage**: Gets unedited, full-resolution image

---

## 📊 API Response Structure

### JSON Response Format

```json
{
  "responses": [
    {
      "landmarkAnnotations": [
        {
          "description": "Eiffel Tower",
          "score": 0.95,
          "locations": [
            {
              "latLng": {
                "latitude": 48.8584,
                "longitude": 2.2945
              }
            }
          ]
        }
      ]
    }
  ]
}
```

### Data Extraction Path

```
responses[0]
  └─ landmarkAnnotations[0]
       ├─ description      → Landmark Name
       ├─ score            → Confidence (0-1)
       └─ locations[0]
            └─ latLng
                 ├─ latitude   → GPS Lat
                 └─ longitude  → GPS Long
```

---

## 🔬 How Google Vision Detects Landmarks

### Behind the Scenes

1. **Image Analysis**: Deep learning models analyze visual features
2. **Pattern Matching**: Compares against database of known landmarks
3. **Geolocation Mapping**: Links detected landmark to GPS coordinates
4. **Confidence Scoring**: Returns match probability (0.0 - 1.0)

### Key Points

- ✅ No GPS/location permissions needed
- ✅ Works with photos from anywhere
- ✅ Database includes 1M+ landmarks worldwide
- ✅ Combines computer vision + geospatial data

---

## ▶️ How to Run

### Prerequisites

```bash
• macOS 13.0+ with Xcode 14+
• iOS 16.0+ device or simulator
• Active internet connection
• Google Vision API key
```

### Steps

1. **Clone Repository**
   ```bash
   git clone https://github.com/yourusername/LandmarkRecognitionApp.git
   cd LandmarkRecognitionApp
   ```

2. **Open Project**
   ```bash
   open LandmarkRecognitionApp.xcodeproj
   ```

3. **Configure API Key**
   - Open `ContentView.swift`
   - Replace `YOUR_GOOGLE_VISION_API_KEY_HERE`

4. **Run App**
   - Select target device/simulator
   - Press `⌘ + R` or click Run
   - Grant photo library access when prompted

5. **Test**
   - Tap "Select Landmark Image"
   - Choose famous landmark (Taj Mahal, Statue of Liberty, etc.)
   - Tap "Detect Landmark"
   - View results! 🎉

---

## 🎯 Supported Landmarks

### Best Recognition

- 🗼 **Towers**: Eiffel Tower, Tokyo Tower, CN Tower
- 🏛️ **Monuments**: Taj Mahal, Statue of Liberty, Christ the Redeemer
- 🏰 **Castles**: Neuschwanstein, Edinburgh Castle
- 🕌 **Religious Sites**: Angkor Wat, Machu Picchu
- 🌉 **Bridges**: Golden Gate, Tower Bridge

### Tips for Best Results

✅ Clear, well-lit images  
✅ Landmark as main subject  
✅ Avoid heavy filters  
✅ Straight-on or iconic angles  
❌ Avoid heavily cropped images  
❌ Avoid photos with text overlays

---

## ⚠️ Common Issues & Solutions

| Issue | Cause | Solution |
|-------|-------|----------|
| "No landmark detected" | Image unclear or unknown landmark | Use famous landmark with clear view |
| "Invalid API key" | Wrong/missing API key | Verify key in Cloud Console |
| "Quota exceeded" | Over 1000 requests/month | Wait for quota reset or upgrade plan |
| "Network error" | No internet connection | Check WiFi/cellular connection |
| Image picker not showing | Missing permissions | Add `NSPhotoLibraryUsageDescription` to Info.plist |

---

## 📱 Required Permissions

Add to `Info.plist`:

```xml
<key>NSPhotoLibraryUsageDescription</key>
<string>We need access to your photos to detect landmarks</string>

<key>NSCameraUsageDescription</key>
<string>We need camera access to capture landmark photos</string>
```

---

## 💡 Use Cases

### Real-World Applications

- 🗺️ **Travel Apps**: Auto-tag photos with landmark names
- 📚 **Education**: Interactive geography learning
- 🎯 **Tourism**: Augmented reality tour guides
- 📸 **Photo Organization**: Automatic album categorization
- 🏆 **Gamification**: Location-based challenges
- 🔍 **Accessibility**: Describe landmarks for visually impaired

---

## 🚀 Future Enhancements

### Planned Features

- [ ] **Map Integration**: Show landmark on Apple Maps
- [ ] **Multiple Landmarks**: Detect all landmarks in image
- [ ] **History Screen**: Save previous detections
- [ ] **Share Feature**: Export results as text/image
- [ ] **Offline Mode**: Cache common landmarks
- [ ] **Camera Integration**: Direct camera capture
- [ ] **AR View**: Overlay landmark info in real-time
- [ ] **Distance Calculator**: Show distance from current location

### Advanced Features

- [ ] Cultural information from Wikipedia API
- [ ] Photo comparison with professional images
- [ ] Similar landmarks suggestions
- [ ] User ratings and reviews integration

---

## 📚 Learning Outcomes

By building this project, you'll master:

- ✅ SwiftUI fundamentals
- ✅ REST API integration
- ✅ JSON parsing in Swift
- ✅ Base64 encoding
- ✅ UIKit-SwiftUI bridging
- ✅ Async networking with URLSession
- ✅ Error handling patterns
- ✅ State management with `@State`
- ✅ Image processing
- ✅ Google Cloud Platform usage

---

## 🔒 Privacy & Security

### Data Handling

- 🔐 Images sent securely via HTTPS
- 🚫 No data stored on Google servers (processed in real-time)
- 🔒 No user tracking or analytics
- ✅ User controls all image selection

### Best Practices

```swift
// ❌ Never do this
let apiKey = "AIzaSyC..." // Hardcoded in source

// ✅ Production approach
let apiKey = ProcessInfo.processEnvironment["VISION_API_KEY"] ?? ""
```

---

## 📊 API Usage Limits

### Free Tier

| Feature | Limit | Cost After |
|---------|-------|------------|
| Requests/Month | 1,000 | $1.50 per 1,000 |
| Requests/Second | 1,800 | N/A |
| Storage | None required | N/A |

### Monitoring Usage

1. Visit [Google Cloud Console](https://console.cloud.google.com)
2. Navigate to **APIs & Services** → **Dashboard**
3. View **Vision API** usage statistics

---
 
## 🙏 Acknowledgments

- [Google Cloud Vision API](https://cloud.google.com/vision) - Landmark detection
- [SwiftUI](https://developer.apple.com/xcode/swiftui/) - Modern UI framework 
