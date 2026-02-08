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

*[Full README content - see previous message for complete text - truncated here for brevity]*

**Ready to master manual photography?**  
**[Download Exposure IQ on the App Store](https://apps.apple.com/app/exposure-iq)** 📸
