# Android Automotive OS (AAOS)

Android Automotive OS (AAOS) is Google's open-source operating system for vehicles. Unlike Android Auto, which projects applications from a smartphone to a vehicle's infotainment display, AAOS runs **directly on the vehicle's hardware** as the car's native operating system.

Because it runs natively inside the vehicle, AAOS can provide traditional infotainment features such as navigation, media playback, and voice assistance while also integrating with vehicle systems like climate control, seat adjustments, cameras, and other vehicle-specific functions through standardized automotive interfaces.

AAOS is built on top of the **Android Open Source Project (AOSP)**, allowing vehicle manufacturers (OEMs) to customize the operating system according to their branding and hardware requirements.

------

# 1. Understanding the Android Automotive Ecosystem

One of the most common sources of confusion is the relationship between **AOSP**, **Android Automotive OS (AAOS)**, and **Google Automotive Services (GAS)**. Although they are closely related, they are not the same.

```text
                 Android Open Source Project (AOSP)
                              │
                              ▼
                 Android Automotive OS (AAOS)
                              │
             ┌────────────────┴────────────────┐
             │                                 │
             ▼                                 ▼
      Without Google Apps           With Google Automotive Services
                                      (Google Maps, Assistant,
                                           Play Store, etc.)
```

## 1.1 Android Open Source Project (AOSP)

The **Android Open Source Project (AOSP)** is the open-source foundation of Android maintained by Google.

It provides:

- The Linux kernel integration
- Android Framework
- System services
- Application runtime
- Hardware Abstraction Layer (HAL)
- Basic Android applications

AOSP is completely open source and can be customized by device manufacturers.

AAOS itself is built on top of AOSP.

------

## 1.2 Android Automotive OS (AAOS)

Android Automotive OS is an automotive-focused implementation of Android that runs directly on the vehicle's hardware.

AAOS extends AOSP by adding automotive-specific components, including:

- Vehicle APIs
- Car Service
- Vehicle Hardware Abstraction Layer (Vehicle HAL or VHAL)
- Driver distraction management
- Multi-display support
- Automotive audio management
- Automotive power management

Unlike Android Auto, AAOS does **not** require a connected smartphone.

Applications execute directly on the vehicle's processors, making the infotainment system independent of the driver's mobile device.

------

## 1.3 Google Automotive Services (GAS)

Google Automotive Services (GAS) is an **optional** collection of proprietary Google applications and services that manufacturers can license for vehicles running AAOS.

GAS typically includes:

- Google Maps
- Google Assistant
- Google Play Store
- Google Play Services

It is important to understand that **Google Automotive Services is not part of AOSP**.

A vehicle can run Android Automotive OS without any Google applications.

For example:

- Volvo and Polestar license Google Automotive Services.
- Some manufacturers choose alternative navigation systems, voice assistants, or application stores instead of Google's ecosystem.

This flexibility is one of AAOS's biggest strengths.

------

# 2. Why Android Automotive OS Was Created

Android Auto successfully brought smartphone applications into vehicles by projecting a simplified interface from the driver's phone onto the infotainment display.

Although this approach works well for navigation, communication, and media playback, it has several limitations:

- It depends on a connected smartphone.
- Vehicle manufacturers have limited control over the user experience.
- Applications cannot directly control vehicle functions.
- Performance depends on the connected phone.
- Offline functionality is limited when the phone is unavailable.

As vehicles evolved into software-defined platforms with increasingly sophisticated digital cockpits, manufacturers required a complete operating system capable of running directly on the vehicle itself.

Android Automotive OS was created to meet these requirements.

Instead of treating the car as an external display, AAOS treats the vehicle as the Android device.

This enables the operating system to interact with vehicle hardware through standardized automotive interfaces while providing a modern application platform for developers.

------

# 3. Why Automakers Choose Android Automotive

Modern vehicles are essentially distributed computer systems containing dozens of Electronic Control Units (ECUs). Manufacturers require an operating system that is scalable, secure, customizable, and capable of integrating infotainment with vehicle functions.

Android Automotive provides these capabilities while leveraging the maturity of the Android ecosystem.

## 3.1 Native Vehicle Integration

AAOS communicates with vehicle hardware through the **Vehicle Hardware Abstraction Layer (Vehicle HAL)**.

This allows applications and system services to access standardized vehicle properties such as:

- Climate control
- Cabin temperature
- Seat position
- Door status
- Fuel or battery level
- Vehicle speed
- Gear position
- Steering wheel controls

Instead of every manufacturer implementing different interfaces for these features, AAOS provides a common software architecture.

------

## 3.2 Familiar Android Development Platform

Because AAOS is based on Android, developers can continue using familiar tools including:

- Android Studio
- Kotlin
- Java
- Gradle
- Android SDK
- Android Debug Bridge (ADB)

This significantly reduces the learning curve for Android developers entering the automotive industry.

------

## 3.3 Rich Application Ecosystem

Vehicles that license **Google Automotive Services** gain access to applications such as Google Maps, Google Assistant, and the Google Play Store.

Manufacturers that do not license GAS can instead integrate their own navigation systems, voice assistants, application stores, or third-party services.

This flexibility allows OEMs to balance user experience, branding, licensing costs, and regional requirements.

------

## 3.4 Customizable User Experience

Unlike Android Auto, where the user interface is largely controlled by Google, AAOS allows manufacturers to customize nearly every aspect of the in-vehicle experience.

OEMs can customize:

- Launcher
- System UI
- Themes
- Vehicle settings
- Instrument cluster integration
- Media applications
- Navigation
- Voice assistant
- Brand identity

As a result, two vehicles running AAOS may look completely different while sharing the same underlying Android platform.

------

## 3.5 Over-the-Air (OTA) Updates

AAOS supports modern software maintenance through Over-the-Air (OTA) updates.

Manufacturers can deliver:

- Security patches
- Feature updates
- Bug fixes
- New applications
- Performance improvements

without requiring customers to visit a dealership.

------

## 3.6 Long-Term Maintainability

Because AAOS is based on Android, manufacturers benefit from:

- A mature software architecture
- Regular Android platform improvements
- A large global developer community
- Continuous security updates
- Extensive documentation and development tools

This reduces development effort while enabling long-term software maintenance throughout the vehicle's lifecycle.

-----------------------------------------------------------------------------------------------------------------------------------

# 4. Android Auto vs Android Automotive OS

Although **Android Auto** and **Android Automotive OS (AAOS)** share the Android name and are both developed by Google, they are fundamentally different technologies designed to solve different problems.

A simple way to think about them is:

- **Android Auto** extends your **smartphone** into your car.
- **Android Automotive OS** turns the **car itself into an Android device**.

Understanding this distinction is essential before learning Android Automotive development.

------

# 4.1 Android Auto

Android Auto is a **projection-based platform** that allows an Android smartphone to display a driver-optimized interface on a vehicle's infotainment screen.

The operating system continues to run on the **smartphone**, while the vehicle primarily acts as an external display and input device.

The phone is responsible for:

- Running applications
- Processing user input
- Accessing mobile data
- GPS navigation
- Media playback
- Voice recognition

The vehicle is responsible for:

- Displaying the interface
- Sending touch input
- Forwarding button events
- Providing microphone and speaker access

Because all application logic runs on the smartphone, disconnecting the phone immediately stops Android Auto.

------

## 4.1.1 How Android Auto Works

The communication flow is illustrated below.

```text
┌───────────────────────────────┐
│        Android Phone          │
│                               │
│  • Android Auto Framework     │
│  • Google Maps                │
│  • Spotify                    │
│  • WhatsApp                   │
│  • Google Assistant           │
└───────────────┬───────────────┘
                │
          USB / Wi-Fi
                │
                ▼
┌───────────────────────────────┐
│ Vehicle Infotainment Display  │
│                               │
│  Receives UI                  │
│  Displays Graphics            │
│  Sends Touch Events           │
└───────────────────────────────┘
```

The car is **not running** Google Maps or Spotify.

Instead, those applications execute entirely on the smartphone.

------

## 4.1.2 User Interaction Flow

When the driver interacts with the infotainment screen:

1. The driver touches the vehicle display.
2. The infotainment system sends the touch event to the phone.
3. The phone processes the event.
4. The application updates its interface.
5. The updated interface is transmitted back to the vehicle.

For example:

```
Driver touches "Navigate"

        ↓

Car sends touch event

        ↓

Phone launches Google Maps

        ↓

Phone renders navigation screen

        ↓

Updated interface appears on vehicle display
```

The vehicle never executes Google Maps itself.

------

## 4.1.3 Advantages of Android Auto

- Familiar Android experience
- Uses the driver's existing applications
- No dedicated vehicle hardware required
- Navigation uses the phone's internet connection
- Applications stay synchronized with the phone

------

## 4.1.4 Limitations of Android Auto

Because Android Auto depends on the smartphone, it has several limitations.

- Requires a compatible Android phone
- Requires USB or wireless connectivity
- Performance depends on the smartphone
- Limited access to vehicle hardware
- Cannot deeply integrate with vehicle systems
- Stops working if the phone disconnects

Android Auto was designed to safely extend smartphone applications into the vehicle—not to control the vehicle itself.

------

# 4.2 Android Automotive OS (AAOS)

Android Automotive OS is a **complete operating system** installed directly on the vehicle.

Instead of projecting applications from a phone, the applications execute natively on the vehicle's processors.

The vehicle becomes an Android device.

This enables AAOS to manage both infotainment features and selected vehicle functions.

Applications can interact with vehicle services through automotive APIs, while lower software layers communicate with vehicle hardware through the **Vehicle Hardware Abstraction Layer (VHAL)**.

------

## 4.2.1 How AAOS Works

Unlike Android Auto, everything executes inside the vehicle.

```text
┌───────────────────────────────┐
│ Android Automotive Apps       │
│                               │
│ Navigation                    │
│ Media                         │
│ Settings                      │
│ Vehicle Apps                  │
└───────────────┬───────────────┘
                │
                ▼
┌───────────────────────────────┐
│ Android Framework             │
└───────────────┬───────────────┘
                │
                ▼
┌───────────────────────────────┐
│ Automotive System Services    │
│ (Car Service, Audio, etc.)    │
└───────────────┬───────────────┘
                │
                ▼
┌───────────────────────────────┐
│ Vehicle HAL (VHAL)            │
└───────────────┬───────────────┘
                │
                ▼
┌───────────────────────────────┐
│ Vehicle ECUs                  │
│ HVAC │ Seats │ Doors │ Battery│
└───────────────────────────────┘
```

Every component runs locally on the vehicle.

A smartphone is optional rather than required.

------

## 4.2.2 Vehicle Integration

One of the biggest advantages of AAOS is its ability to communicate with vehicle hardware.

Typical vehicle functions include:

- Climate control
- Seat adjustment
- Steering wheel buttons
- Camera systems
- Instrument cluster
- Battery status
- Fuel level
- Vehicle speed
- Door status
- Lighting controls

Rather than communicating directly with hardware, applications use Android Automotive APIs.

Those APIs communicate with **Car Service**, which accesses vehicle properties through the **Vehicle HAL (VHAL)**.

This architecture separates application developers from hardware-specific implementations.

------

## 4.2.3 Smartphone Support

AAOS does **not** require a smartphone.

However, smartphones can still be connected for additional functionality such as:

- Bluetooth calls
- Contact synchronization
- Media streaming
- Android Auto (if supported by the manufacturer)
- Apple CarPlay (if supported)

This means a vehicle running AAOS may still support Android Auto, but the two technologies operate independently.

------

## 4.2.4 Advantages of AAOS

- Native execution on vehicle hardware
- Deep vehicle integration
- Highly customizable user interface
- Automotive-specific APIs
- OTA software updates
- Independent of smartphones
- Supports multiple displays
- Designed for long-term vehicle software maintenance

------

## 4.2.5 Limitations

AAOS also introduces new challenges.

- Requires significantly more powerful vehicle hardware
- Higher development cost
- Longer validation and certification process
- Responsibility for long-term software maintenance
- OEMs must integrate with numerous vehicle ECUs

Despite these challenges, AAOS has become the preferred platform for many modern software-defined vehicles.

------

# 4.3 Android Auto vs Android Automotive OS

| Feature                       | Android Auto          | Android Automotive OS                  |
| ----------------------------- | --------------------- | -------------------------------------- |
| Runs on                       | Smartphone            | Vehicle                                |
| Smartphone Required           | ✅ Yes                 | ❌ No                                   |
| Processing Location           | Smartphone            | Vehicle                                |
| Requires USB/Wi-Fi            | Usually Yes           | No                                     |
| Can Control Vehicle Functions | Limited               | Yes (through automotive APIs and VHAL) |
| Uses Vehicle Hardware         | Minimal               | Extensive                              |
| Works Without Phone           | No                    | Yes                                    |
| Can Run Google Maps           | Via Phone             | Native app (if GAS is licensed)        |
| Supports Google Play Store    | No                    | Optional (through GAS)                 |
| OEM Customization             | Limited               | Extensive                              |
| Supports OTA Updates          | Phone updates apps    | Vehicle software and apps              |
| Primary Purpose               | Smartphone projection | Native vehicle operating system        |

--------------------------------------------------------------------------------------------------------------

# 5. Android Automotive OS in Production Vehicles

Android Automotive OS (AAOS) has been adopted by an increasing number of automotive manufacturers. Although every manufacturer builds on the same Android platform, the resulting infotainment systems can differ significantly.

Some manufacturers license **Google Automotive Services (GAS)** to provide Google Maps, Google Assistant, and the Google Play Store, while others choose to build their own ecosystem using third-party or proprietary services.

This flexibility is one of AAOS's greatest strengths.

------

# 5.1 Two Deployment Models

Before looking at specific manufacturers, it is important to understand that there are two common ways to deploy AAOS.

## Option 1: AAOS + Google Automotive Services (GAS)

In this model, the vehicle runs Android Automotive OS and includes Google's proprietary automotive services.

Typical features include:

- Google Maps
- Google Assistant
- Google Play Store
- Google Play Services
- Google Voice Search

This provides an experience that feels familiar to Android users.

```text
      Android Automotive OS
               │
               ▼
      Google Automotive Services
               │
      Google Maps
      Google Assistant
      Google Play Store
```

------

## Option 2: AAOS Without GAS

Manufacturers may also deploy AAOS without licensing Google Automotive Services.

In this case, they typically provide:

- Their own navigation system
- Their own voice assistant
- Their own application store
- Third-party cloud services
- Customized infotainment applications

This approach gives manufacturers complete control over branding, user experience, and regional service providers.

```text
      Android Automotive OS
               │
               ▼
      Manufacturer Services
               │
      Navigation
      Voice Assistant
      App Store
      Connected Services
```

------

# 5.2 Volvo Cars

Volvo was one of the first major manufacturers to adopt Android Automotive OS in production vehicles.

Its infotainment system is built on AAOS and includes **Google Automotive Services**, giving drivers access to:

- Google Maps
- Google Assistant
- Google Play Store

Because the operating system is deeply integrated with the vehicle, users can control functions such as climate settings, navigation, media, and vehicle settings from a unified interface.

Examples include:

- XC40 Recharge
- C40 Recharge
- EX30
- EX90
- XC60 (newer model years)

Volvo also receives over-the-air software updates that continuously improve the infotainment experience.

------

# 5.3 Polestar

Polestar, which originated as Volvo's performance brand before becoming a standalone manufacturer, also uses Android Automotive OS with Google Automotive Services.

The **Polestar 2** was one of the first production vehicles designed around AAOS.

Its infotainment system demonstrates how Android Automotive can provide a modern, responsive user experience while maintaining deep integration with vehicle systems.

Features include:

- Native Google Maps navigation
- Google Assistant
- Google Play Store
- OTA software updates
- Integrated vehicle settings

Polestar has become one of Google's closest automotive partners and is often used to demonstrate new AAOS capabilities.

------

# 5.4 General Motors (GM)

General Motors announced a long-term partnership with Google to bring Android Automotive OS to multiple vehicle brands.

Many recent GM vehicles include:

- Android Automotive OS
- Google Maps
- Google Assistant
- Google Play Store

Brands include:

- Chevrolet
- GMC
- Cadillac
- Buick

GM customizes the user interface to match each vehicle brand while continuing to use Android as the underlying operating system.

------

# 5.5 Renault Group

The Renault Group, including Renault, Dacia, and Alpine, has adopted Android Automotive OS for many of its newer vehicles.

Several models include Google Automotive Services, allowing drivers to use familiar Google applications directly from the infotainment system.

The Renault implementation demonstrates one of AAOS's key advantages:

Although every manufacturer starts with the same Android platform, each can create a unique user interface and driving experience.

------

# 5.6 Other Manufacturers Using AAOS

The adoption of Android Automotive continues to grow across the automotive industry.

Manufacturers that have announced or deployed AAOS in at least some vehicle platforms include:

- Honda
- Nissan
- Mitsubishi
- Ford
- Lincoln
- Porsche
- Audi
- Lucid Motors

Not every model from these manufacturers uses AAOS. Deployment varies by vehicle platform, model year, and market.

------

# 5.7 Manufacturers That Do Not Use Google Automotive Services

Although Google Automotive Services is popular, several manufacturers choose alternative ecosystems.

Reasons include:

- Regional regulations
- Business strategy
- Existing software investments
- Partnerships with other technology companies
- Greater control over user experience

Instead of Google services, manufacturers may integrate:

- HERE Technologies
- TomTom
- Amazon Alexa
- Cerence Assistant
- Proprietary navigation systems
- Custom application stores

This demonstrates an important principle:

**Android Automotive OS does not depend on Google's applications.**

AAOS provides the operating system, while manufacturers decide which services are installed.

------

# 5.8 Why Manufacturers Choose Different Solutions

The choice between using GAS and building a custom ecosystem depends on several factors.

| Consideration        | AAOS + GAS                 | AAOS without GAS            |
| -------------------- | -------------------------- | --------------------------- |
| Google Maps          | ✓                          | Optional alternative        |
| Google Assistant     | ✓                          | Alternative voice assistant |
| Google Play Store    | ✓                          | Custom app store            |
| Google Play Services | ✓                          | Not included                |
| User Experience      | Familiar Android ecosystem | Fully customized            |
| Licensing            | Requires Google agreement  | No Google licensing         |
| OEM Branding         | Moderate customization     | Maximum customization       |

Neither approach is inherently better.

Manufacturers select the solution that best aligns with their technical requirements, business strategy, target markets, and customer expectations.

