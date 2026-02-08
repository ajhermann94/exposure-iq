# Exposure IQ

**Professional DSLR & Mirrorless Camera Companion for iOS**

[![iOS](https://img.shields.io/badge/iOS-17.0+-blue.svg)](https://www.apple.com/ios/)
[![Swift](https://img.shields.io/badge/Swift-5.9+-orange.svg)](https://swift.org/)
[![License](https://img.shields.io/badge/License-Proprietary-red.svg)]()
[![App Store](https://img.shields.io/badge/App%20Store-Download-blue.svg)](https://apps.apple.com/app/exposure-iq)

## 📸 Overview

Exposure IQ is an intelligent camera companion app that provides **real-time exposure recommendations** for DSLR and mirrorless photographers shooting in manual mode. Using the proprietary **APEX-CAL™ constraint-solver engine**, it calculates optimal ISO, aperture, and shutter speed settings based on scene analysis, photography mode, and gear capabilities.

**[Download on the App Store](https://apps.apple.com/app/exposure-iq)** • **[Documentation](https://ajhermann94.github.io/exposure-iq/)** • **[Developer: @ajhermann94](https://github.com/ajhermann94)**

---

## 🎯 Problem Statement

Manual photography requires simultaneously optimizing three interdependent camera settings while respecting:

- **Logarithmic mathematics** (base-2 stop-space: doubling ISO = +1 stop)
- **Physical constraints** (lens aperture range, camera ISO limits, stabilization capabilities)  
- **Creative priorities** (shallow DOF vs. sharpness vs. low noise)
- **Scene dynamics** (brightness range from EV 3 to EV 16 = 8,192× adjustment)

Traditional exposure calculators use heuristic compensation or reference points. **Exposure IQ uses deterministic constraint-solving** with the fundamental APEX equation:
```
Scene EV = Av + Tv + Sv

Where:
  Av = 2·log₂(f-number)    [Aperture Value]
  Tv = log₂(1/t)            [Time Value]  
  Sv = log₂(ISO/100)        [Sensitivity Value]
```

Given scene EV and mode priorities, the solver **algebraically calculates** settings that satisfy the exposure equation while respecting gear constraints and creative intent.

---

## ✨ Key Features

### 🧮 APEX-CAL™ Constraint Solver
- **Deterministic calculations**: Same scene always produces same recommendations
- **Stop-space mathematics**: Pure logarithmic (base-2) computations, no linear approximations
- **Hierarchical constraints**: Anchor (creative) → Helper (technical) → Release valve (absorbs deficit)
- **Violation detection**: Color-coded warnings when physical limits are hit
- **Gear recommendations**: Suggests specific equipment when current setup can't achieve the shot

### 📷 Photography Modes (10 Total)

| Mode | Anchor | Helper | Release Valve | Use Case |
|------|--------|--------|---------------|----------|
| **General** | None | Shutter, Aperture | ISO | Balanced point-and-shoot |
| **Portrait** | Aperture (f/1.4-f/2.8) | Shutter (1/125s) | ISO | Shallow DOF, bokeh |
| **Landscape** | Aperture (f/8-f/16) | ISO (100-200) | Shutter | Maximum sharpness |
| **Action** | Shutter (1/500-1/2000s) | Aperture (f/2.8-f/5.6) | ISO | Freeze motion |
| **Low Light** | Aperture (wide open) | Shutter (safe min) | ISO | Indoor/dim scenes |
| **Macro** | Aperture (f/8-f/16) | Shutter (1/125s) | ISO | Close-up detail |
| **Astrophotography** | Aperture (widest) + Shutter (500-rule) | None | ISO | Night sky, stars |
| **Night Photography** | Aperture (wide open) | None | ISO | City lights, long exposure |
| **Aperture Priority** | Aperture (user-set) | Shutter (flexible) | ISO | Manual DOF control |
| **Shutter Priority** | Shutter (user-set) | Aperture (flexible) | ISO | Manual motion control |

### 🤖 Real-Time Scene Analysis
- **Face detection**: Adjusts aperture based on subject count and distance (depth-aware DOF)
- **Scene classification**: Recognizes outdoor, indoor, low-light scenarios via Vision framework
- **Motion detection**: Triggers action mode recommendations for moving subjects
- **Brightness measurement**: Extracts EXIF BrightnessValue from camera sensor

### 🎒 Gear-Aware Calculations
- **Lens library**: 200+ lenses with focal length, max aperture, OIS capabilities
- **Camera bodies**: Full-frame, APS-C, Micro 4/3 sensor formats with crop factor adjustments
- **Stabilization**: IBIS/OIS-aware shutter speed calculations (relaxed hand-holding rule)
- **Physical constraints**: Respects lens aperture range, camera ISO limits

### 📊 Advanced Features (Pro)
- **Lens recommendations**: "Switch to 50mm f/1.8 for +1.9 stops" when gear-limited
- **Solar angle calculator**: Golden hour timing for landscape photography
- **Coaching tips**: Educational guidance explaining why settings are recommended
- **Constraint violation warnings**: Yellow (technical concern) vs. Red (impossible shot)

---

## 🏗️ Technical Architecture

### Core Components
```
ExposureConstraintSolver.swift   (400 lines)  - APEX-CAL™ engine, stop-space math
SceneAnalyzer.swift              (600 lines)  - Vision framework, face/scene detection
CameraService.swift              (500 lines)  - AVFoundation camera interface
GearLibrary.swift                (300 lines)  - Lens/body database, constraint lookup
```

### Technology Stack

- **Language**: Swift 5.9+ with SwiftUI for reactive UI
- **Frameworks**:
  - `AVFoundation` - Camera capture and EXIF metadata extraction
  - `Vision` - Face detection, scene classification (on-device ML)
  - `CoreML` - ResNet50 scene understanding model
  - `CoreLocation` - Optional solar angle calculations
  - `Combine` - Reactive programming for state management
- **Architecture**: MVVM with `@Published` properties, Combine publishers
- **Performance**: 
  - Sub-millisecond solver execution (<0.5ms on iPhone 12)
  - 2-4 FPS scene analysis (optimized intervals)
  - Debounced UI updates (300ms) for smooth rendering

### Mathematical Foundation

The constraint solver operates in **logarithmic stop-space** to avoid cumulative floating-point errors:
```swift
// Convert physical values to stops
let av = 2.0 * log2(fNumber)           // f/2.8 → 5.0 stops
let tv = log2(1.0 / shutterSeconds)    // 1/125s → 6.97 stops  
let sv = log2(iso / 100.0)             // ISO 640 → 2.68 stops

// Exposure equation MUST balance
assert(abs((av + tv + sv) - sceneEV) < 0.1)
```

**Constraint hierarchy example** (Portrait mode in low light):

1. **Anchor** (Aperture): f/1.8 is locked (creative priority)
2. **Helper** (Shutter): 1/125s minimum for sharpness → **violated** if scene needs slower
3. **Release valve** (ISO): Absorbs remaining deficit → ISO 3200 if helper violated

If anchor constraint is violated (e.g., f/1.4 needed but lens only goes to f/1.8), app shows **red warning** and recommends faster lens.

---

## 🚀 Installation & Usage

### Requirements
- iOS 17.0 or later
- iPhone 12 or newer (optimized for performance)
- Camera permissions (required for scene analysis)
- Optional: Location permissions (for solar angle feature)

### Download
**[Get Exposure IQ on the App Store](https://apps.apple.com/app/exposure-iq)**

### Quick Start
1. Launch app and grant camera permissions
2. Select photography mode (General, Portrait, Landscape, Action, Low Light, Macro, Astrophotography, Night Photography, Aperture Priority, Shutter Priority)
3. Point camera at scene - see real-time exposure recommendations
4. Configure gear library (Settings → Camera Body → Add Lenses)
5. Watch for color-coded warnings:
   - **Green**: All constraints satisfied ✓
   - **Yellow**: Technical concern (e.g., slow shutter) ⚠️
   - **Red**: Physical limitation (need different gear) 🚫

---

## 💰 Pricing & Business Model

**Freemium with 3-Day Trial**

### Free Forever
- **General photography mode** with full exposure calculations
- Real-time recommendations (ISO, aperture, shutter)
- Basic scene analysis
- No ads, no data collection

### Pro Subscription
- **$1.99/month** or **$14.99/year** (37% savings)
- **3-day free trial** with all Pro features unlocked
- Includes:
  - Portrait, Landscape, Action, Low Light, Macro, Astrophotography, Night Photography, Aperture Priority, Shutter Priority modes
  - Face detection with depth-aware aperture
  - Lens recommendation system
  - Solar angle calculator
  - Advanced coaching tips
  - Gear library management

---

## 🔒 Privacy Policy

**Privacy-First Architecture** - No data collection, all processing on-device.

### What We DON'T Collect
- ❌ No photos stored or transmitted
- ❌ No location tracking (solar angle uses one-time optional query)
- ❌ No analytics SDKs (Firebase, Mixpanel, etc.)
- ❌ No advertising IDs (IDFA)
- ❌ No third-party tracking or servers
- ❌ No account creation (email, passwords, user profiles)

### On-Device Processing
- Camera analysis: `AVFoundation` (Apple framework)
- Face detection: `Vision` (Apple on-device ML)
- Scene classification: `CoreML` (Apple on-device ML)
- Exposure calculations: Pure Swift (no external dependencies)

### Fully Offline
App is **100% functional offline** after initial App Store download. No internet connection required for any feature.

**Questions?** Contact [developer](https://github.com/ajhermann94)

---

## 📖 Documentation

### For Users
- **[App Website](https://ajhermann94.github.io/exposure-iq/)** - Features, pricing, FAQ
- **In-App Coaching** - Educational tips explaining recommendations

### For Developers
- **[README.md](README.md)** - This file (architecture overview)
- **Source Code** - Proprietary (closed-source)
- **APEX Standard** - Mathematical foundation based on ANSI PH2.7-1986

---

## 🎓 Use Cases

### 📚 Photography Students
Learn exposure triangle relationships through real-time feedback. See how changing one parameter affects others. Coaching tips explain the "why" behind recommendations.

### 🎬 DSLR Video Shooters  
Maintain proper shutter speed for motion blur (180° rule: 1/48s @ 24fps). Calculate ND filter strength needed. Monitor exposure during takes.

### 🏃 Event Photographers
Quick exposure checks when moving between indoor/outdoor. Action mode for sports, Portrait mode for posed shots. Gear recommendations when lighting gets challenging.

### 🌄 Landscape Photographers
Calculate hyperfocal distance for maximum sharpness. Determine if scene requires exposure bracketing. Check if ND filter needed for long exposures.

### 🌃 Low Light Photographers
Indoor photography without flash. Restaurants, bars, museums, twilight scenes. App prioritizes wide aperture, accepts higher ISO.

### 🌌 Astrophotographers
Milky Way, star trails, night sky landscapes. Dual anchors (widest aperture + 500-rule shutter) prevent star trails while gathering maximum light.

### 🏙️ Night Scene Photographers
City lights, skylines, light painting, fireworks, blue hour. Allows 30s exposures on tripod, prioritizes wide aperture.

### 🎛️ Aperture Priority Users
Learning depth-of-field control. Set exact f-stop, app calculates shutter/ISO. Transitioning from auto to full manual.

### ⏱️ Shutter Priority Users  
Creative motion control. Set exact shutter (freeze sports at 1/2000s, blur waterfalls at 1/4s). App handles aperture/ISO.

### 📸 Manual Mode Learners
Transitioning from auto to manual? See what camera's auto-exposure would choose, understand trade-offs as you adjust.

---

## 🤔 FAQ

**Q: How accurate are the recommendations?**  
A: Within 1/3 stop of professional manual calculations. Uses same APEX mathematics as cameras internally.

**Q: Does this replace my camera's light meter?**  
A: No, it's a **companion tool**. Your meter measures the scene, Exposure IQ calculates optimal settings for creative intent.

**Q: Can I use this for film photography?**  
A: Yes! Exposure triangle math is identical for film. Works as spot meter + exposure calculator for any manual camera.

**Q: Why high ISO recommendations sometimes?**  
A: In Portrait mode, if aperture (f/1.8) and shutter (1/125s) are locked, ISO is the only adjustment. Low light might require ISO 3200+. App suggests faster lens as alternative.

**Q: What's the difference between yellow/red warnings?**  
A: **Yellow** = Technical concern (slow shutter, photo still possible). **Red** = Physical impossibility (need different gear).

**Q: What's the difference between Night Photography and Astrophotography?**  
A: **Night Photography** is for city lights, long exposures, light painting. Allows 30s shutters. **Astrophotography** uses 500-rule to prevent star trails, specifically for stars and Milky Way.

**Q: When should I use Aperture Priority or Shutter Priority?**  
A: **Aperture Priority** when depth-of-field is your creative focus. **Shutter Priority** when motion control is your goal. Both let you learn manual exposure by controlling one parameter.

---

## 🛠️ Development

### Architecture Evolution

Exposure IQ represents a **complete architectural rewrite** (January 2025):

**Before**: Heuristic compensation-based system
- Used arbitrary reference points (EV 10.0)
- Compensation values adjusted ISO/aperture/shutter
- Could produce mathematically impossible settings (exposure equation violated by 9+ stops)
- ~500 lines of error-prone conditional logic

**After**: Deterministic constraint-solver (APEX-CAL™)
- Scene EV is single source of truth (no reference points)
- Pure stop-space logarithmic calculations
- Exposure equation **always** satisfied (mathematical proof)
- Hierarchical constraint priority with violation detection
- ~400 lines of rigorous solver code with 100% test coverage

### Performance Optimizations (February 2025)
- Reduced Vision framework analysis from 10 FPS → 2-4 FPS (50% CPU reduction)
- Debounced UI updates from 60ms → 300ms (smoother rendering)
- Lazy face detection (only when Portrait mode active)
- Extended scene brightness change threshold (0.3 EV → 0.5 EV)

---

## 👨‍💻 Developer

**Alan Hermann** ([@ajhermann94](https://github.com/ajhermann94))

- **Portfolio**: [GitHub Profile](https://github.com/ajhermann94)
- **Contact**: Via GitHub issues or profile
- **Support**: Bug reports and feature requests welcome

---

## 📜 License

**Proprietary Software** - All rights reserved.

Exposure IQ is closed-source commercial software. The mathematical APEX standard (ANSI PH2.7-1986) is public domain, but the constraint-solver implementation is proprietary.

---

## 🙏 Acknowledgments

- **APEX Standard** (ANSI PH2.7-1986) - Additive System of Photographic Exposure
- **Apple Vision Framework** - On-device ML for face detection
- **Photography Community** - Feedback on professional workflows and constraint hierarchies

---

**Ready to master manual photography?**  
**[Download Exposure IQ on the App Store](https://apps.apple.com/app/exposure-iq)** 📸
