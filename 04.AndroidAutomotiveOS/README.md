# Android Automotive OS (AAOS)

> **Evidence:** platform claims were checked against **AOSP `android-17.0.0_r1`** (paths given).
> Market and OEM information is **external and changes quickly**. It was checked against public
> sources in November 2026 (listed at the end), and each OEM's current status should be confirmed on
> Google's official "Google built-in" pages before you rely on it.

| Label | Meaning |
|---|---|
| **Verified in source** | Read in the AOSP 17 tree |
| **Official documentation** | source.android.com / developer.android.com / OEM press release |
| **External (market)** | Press/industry reporting; may change |

**Prerequisites:** [Digital Cockpits](../00.DigitalCockpits/README.md), [Android Auto](../03.AndroidAuto/README.md).
**Next:** [Android Architecture](../05.AndroidArchitecture/README.md) → [AAOS Architecture](../06.AndroidAutomotiveArchitecture/README.md).

**After this chapter you should be able to:**

1. Separate **AOSP**, **AAOS** and **GAS** precisely, and say what is open source and what is licensed.
2. Explain why AAOS was created and what it adds to AOSP, with the real source directories.
3. Compare Android Auto and AAOS on every important dimension.
4. Explain the two deployment models (with and without GAS) using real OEM examples.

---

## Table of Contents

- [0. What is AAOS?](#0-what-is-aaos)
- [1. AOSP vs AAOS vs GAS](#1-aosp-vs-aaos-vs-gas)
- [2. Why AAOS was created](#2-why-aaos-was-created)
- [3. Why automakers choose AAOS](#3-why-automakers-choose-aaos)
- [4. Android Auto vs Android Automotive OS](#4-android-auto-vs-android-automotive-os)
- [5. AAOS in production vehicles](#5-aaos-in-production-vehicles)
- [6. Common misconceptions](#6-common-misconceptions)

---

## 0. What is AAOS?

**Android Automotive OS (AAOS)** is a version of Android that runs **directly on the vehicle's
infotainment hardware** as the car's own operating system. **Android Auto**, by contrast, runs on
your phone and *projects* a UI onto the car screen ([Android Auto](../03.AndroidAuto/README.md)).

Because AAOS runs natively in the car, it can:

- provide normal infotainment: media, navigation, voice, apps;
- integrate with **vehicle functions** (climate, seats, vehicle settings, cameras) through a
  standard vehicle interface, the **Vehicle HAL**;
- be fully **branded and customized** by the car maker (OEM).

AAOS is built from **AOSP** (the open-source Android code base). The automotive parts are open source too ([§1.2](#12-android-automotive-os-aaos)).

---

## 1. AOSP vs AAOS vs GAS

This is the most common source of confusion. They are related but **not the same thing**.

```text
                 Android Open Source Project (AOSP)
                      open source, all device types
                              │
                              ▼
                 Android Automotive OS (AAOS)
          AOSP + automotive components (also open source)
                              │
             ┌────────────────┴────────────────┐
             │                                 │
             ▼                                 ▼
      AAOS without GAS                  AAOS + Google Automotive Services (GAS)
  (OEM / 3rd-party services)            ("Google built-in": Maps, Assistant,
                                          Play Store, Play services — licensed)
```

### 1.1 Android Open Source Project (AOSP)

**What:** the open-source Android code base: kernel integration, `init`, native daemons, ART,
framework, system services, HAL interfaces, core apps, build system and tests.

**Open source:** yes. Anyone can download, modify and build it. AAOS is built on it.

Details: [Android Architecture](../05.AndroidArchitecture/README.md).

### 1.2 Android Automotive OS (AAOS)

**What:** AOSP **plus** the automotive components, enabled by building an automotive product. The
automotive pieces are **open source in AOSP** (Verified in source):

| Component | What it does | AOSP location |
|---|---|---|
| **Car API** (`android.car`) | Java APIs for apps: vehicle properties, power, UX restrictions… | `packages/services/Car/car-lib/` |
| **CarService** | Automotive system service process | `packages/services/Car/service/`, `service-builtin/` |
| **CarServiceHelperService** | Glue inside `system_server` | `frameworks/opt/car/services/` |
| **Vehicle HAL** (interface + reference impl) | The car as standard **vehicle properties** | `hardware/interfaces/automotive/vehicle/` |
| **Driver distraction / UX restrictions** | Limits UI while driving | `.../service/src/com/android/car/CarUxRestrictionsManagerService.java` |
| **Multi-display / occupant zones** | Driver, passenger and rear-seat displays and users | `.../CarOccupantZoneService.java` |
| **Car audio** | Audio zones, volume groups, focus for car sounds | `.../service/src/com/android/car/audio/CarAudioService.java` |
| **Car power management** | Vehicle power states, suspend/hibernation, power policies | `.../power/CarPowerManagementService.java`, `packages/services/Car/cpp/power/` |
| **EVS (Exterior View System)** | Fast rear-view / surround cameras | `packages/services/Car/cpp/evs/` |
| **Projection support** | Lets AAOS act as an Android Auto *receiver* when the OEM integrates it | `.../CarProjectionService.java` |
| **Car system apps** | CarLauncher, CarSystemUI, CarSettings, SystemUpdater… | `packages/apps/Car/` |

**How a build becomes "automotive" (Verified in source):** automotive products inherit
`packages/services/Car/car_product/build/car_base.mk`. Line 105 installs
`android.hardware.type.automotive.xml`, which declares the **`android.hardware.type.automotive`**
feature. `SystemServer` checks that feature to start CarServiceHelperService
([Boot §7](../07.AndroidBootingSequence/README.md#7-aaos-specific-how-carservice-is-started)).
Ready-made automotive targets in AOSP include `sdk_car_x86_64` and `sdk_car_arm64` (emulator,
`device/generic/car/`), Cuttlefish auto, and your RPi5 `aosp_rpi5_car`.

**No smartphone required.** Apps execute on the vehicle's own processor.

### 1.3 Google Automotive Services (GAS)

**What:** an **optional, proprietary, licensed** package of Google apps and services for AAOS
vehicles, marketed as **"Google built-in"**. It typically includes:

- **Google Maps**;
- **Google Assistant** (being replaced by Gemini in newer releases, External);
- **Google Play Store** (car-optimized apps);
- **Google Play services**.

**GAS is not part of AOSP.** You will not find it in your source trees. An OEM must sign an
agreement with Google and pass compatibility testing (CTS etc.) to ship it.

**A vehicle can run AAOS without any Google apps**: see the BMW example in
[§5.5](#55-aaos-without-gas-examples).

---

## 2. Why AAOS was created

**The situation before:** Android Auto ([chapter 03](../03.AndroidAuto/README.md)) brought phone
apps into cars by **projecting** the phone's UI. That works well for navigation, media and messaging,
but it has structural limits:

| Limitation of projection | Consequence |
|---|---|
| Depends on a connected, compatible phone | No phone, low battery or a bad cable means no apps |
| OEM controls little of the UX | The UI looks the same in every brand |
| Apps cannot access vehicle functions | Climate, seats and vehicle settings stay in a separate OEM system |
| Performance depends on the phone | Old phones give a laggy experience |
| Two systems on screen | The driver switches between Android Auto and the car's native UI |

**What changed:** cars became **software-defined** with digital cockpits
([chapter 00](../00.DigitalCockpits/README.md)). OEMs needed a full, maintainable OS on the head
unit itself, with a modern app platform.

**The AAOS idea:** instead of treating the car as an **external display**, treat the **car itself
as the Android device**. The infotainment SoC runs Android, and vehicle functions are reached through a
standard, permission-controlled interface (VHAL → CarService → Car API).

---

## 3. Why automakers choose AAOS

### 3.1 Native vehicle integration through a standard interface

The **Vehicle HAL** represents the car as standard **vehicle properties** with fixed IDs, types and
units (Verified in source,
`hardware/interfaces/automotive/vehicle/aidl_property/android/hardware/automotive/vehicle/VehicleProperty.aidl`):

| Vehicle function | Example property |
|---|---|
| Climate control / cabin temperature | `HVAC_TEMPERATURE_SET`, `HVAC_FAN_SPEED` |
| Doors | `DOOR_LOCK` |
| Speed | `PERF_VEHICLE_SPEED` |
| Gear | `GEAR_SELECTION` |
| Ignition | `IGNITION_STATE` |
| Identity | `INFO_VIN` |

Instead of every OEM inventing its own interface, apps and services use one architecture.
Deep dive: [AAOS Architecture §3](../06.AndroidAutomotiveArchitecture/README.md#3-vehicle-properties-in-depth).

### 3.2 Familiar development platform (two different audiences)

| Audience | Tools | Notes |
|---|---|---|
| **App developers** (third-party or OEM apps) | Android Studio, Kotlin/Java, **Gradle**, Android SDK, the `android.car` library, the androidx **Car App Library**, the AAOS emulator, `adb` | Same skills as phone apps, plus car UX rules |
| **Platform developers** (OEM / Tier-1 engineers building the OS) | AOSP source, **Soong (Android.bp) + Make**, `lunch`/`m`, Cuttlefish, CTS/VTS, `adb`, `fastboot` | Gradle is **not** used to build the platform |

### 3.3 Rich application ecosystem

- **With GAS:** Google Maps, Google Assistant and the Play Store with car-optimized apps.
- **Without GAS:** the OEM provides its own navigation, voice assistant and app store, for example
  through a third-party store provider ([§5.5](#55-aaos-without-gas-examples)).

### 3.4 Customizable user experience

OEMs can customize nearly everything: launcher, system UI, themes, settings, cluster integration,
media, navigation, voice and brand identity. Two AAOS cars can look completely different while
sharing the same platform. The customization points are open source in `packages/apps/Car/`
(Launcher, SystemUI, Settings), with resource overlays (RROs) for theming.

### 3.5 Over-the-air (OTA) updates

AOSP includes the update stack: **A/B (seamless) updates** with `update_engine` (`system/update_engine/`)
and a car **SystemUpdater** app (`packages/apps/Car/SystemUpdater/`). OEMs deliver security patches,
features, fixes and app updates without a dealer visit. **How** updates are downloaded, scheduled and
authorized (for example only while parked) is the OEM's backend and policy.

### 3.6 Long-term maintainability

- A mature, documented architecture with large community knowledge;
- regular Android platform releases and monthly security bulletins;
- **Treble/VINTF** separation of vendor and system ([Android Architecture §3](../05.AndroidArchitecture/README.md#3-hardware-abstraction-layer-hal)),
  so the platform can be upgraded with less vendor rework.

This matters because cars stay on the road for 10–15 years, much longer than phones.

---

## 4. Android Auto vs Android Automotive OS

A simple way to remember:

- **Android Auto** extends your **phone** into the car.
- **Android Automotive OS** turns the **car itself into an Android device**.

### 4.1 Android Auto (projection)

The OS and apps run on the **phone**. The car screen is mainly a **display and input device**.

| Phone does | Car head unit does |
|---|---|
| Runs the apps, processes input, uses mobile data and GPS, plays media, does voice recognition, **renders the UI** | **Shows** the projected video, **sends** touch/knob/button input, provides **microphone/speakers** and car sensor data |

```text
┌───────────────────────────────┐
│ Android phone                 │
│  Android Auto app + apps      │
│  (Maps, Spotify, WhatsApp…)   │
└───────────────┬───────────────┘
                │  USB or Wi-Fi (Android Auto protocol)
                ▼
┌───────────────────────────────┐
│ Car head unit                 │
│  receiver software:           │
│  displays video, sends input, │
│  routes audio                 │
└───────────────────────────────┘
```

**Interaction flow:**

1. The driver taps "Navigate" on the car screen.
2. The car sends the touch event to the phone.
3. The phone launches/updates Google Maps and **renders** the new screen.
4. The phone sends the frame back as **video**, and the car displays it.

The car **never executes** Google Maps itself. Disconnecting the phone ends the session.

**Advantages:** uses the driver's own apps and accounts, needs no Google agreement for app content in
the car, and apps stay in sync with the phone.
**Limitations:** see [§2](#2-why-aaos-was-created) and [chapter 03 §4](../03.AndroidAuto/README.md#4-drawbacks-of-android-auto).

### 4.2 Android Automotive OS (native)

Everything executes **in the car**. The correct process view (Verified in source; details in
[AAOS Architecture §0](../06.AndroidAutomotiveArchitecture/README.md#0-the-big-picture)):

```text
┌───────────────────────────────────────────────┐
│ App process (e.g. CarSystemUI HVAC panel)     │
│   app code + Car API library (android.car)    │
└───────────────────────┬───────────────────────┘
                        │ Binder IPC
                        ▼
┌───────────────────────────────────────────────┐
│ CarService process (com.android.car)          │
│   permission checks, CarPropertyService, …    │
└───────────────────────┬───────────────────────┘
                        │ Binder IPC (IVehicle AIDL)
                        ▼
┌───────────────────────────────────────────────┐
│ Vehicle HAL process (vendor)                  │
└───────────────────────┬───────────────────────┘
                        │ vendor-specific (MCU / gateway / CAN / Ethernet)
                        ▼
┌───────────────────────────────────────────────┐
│ Vehicle ECUs: HVAC │ seats │ doors │ battery  │
└───────────────────────────────────────────────┘
```

> Car API is **not** a separate layer below the app. It is a **library inside the app's process**, and CarService is a **separate process**, not part of the generic "Android Framework" box.

**Smartphones are optional.** An AAOS car can still use a phone for:

- Bluetooth calls, contacts and messages (`packages/apps/Car/` Dialer/Messenger + Bluetooth profiles);
- media streaming;
- **Android Auto or Apple CarPlay projection**, *if the OEM integrates a receiver*. AOSP provides
  hooks such as `CarProjectionService`, but the Android Auto receiver itself is a Google component.

**Advantages:** native execution, deep vehicle integration, OEM-customizable UX, automotive APIs,
independence from phones, multi-display, OTA for the whole system.
**Challenges:** more capable (and costly) head-unit hardware, long validation, OEM responsibility for
**years** of updates, and integration with many ECUs through the VHAL.

### 4.3 Comparison table

| Feature | Android Auto | Android Automotive OS |
|---|---|---|
| Where the OS and apps run | Phone | Vehicle |
| Phone required | ✅ Yes | ❌ No (optional) |
| USB/Wi-Fi link to phone | Required | Not required |
| Access to vehicle functions | Very limited (some sensor data for apps like navigation) | Yes, through Car API → CarService → **VHAL**, permission-controlled |
| Uses vehicle compute | Minimal (decode video, input) | Full (runs the OS) |
| Works without a phone | No | Yes |
| Google Maps | From the phone | Native, **only if GAS is licensed** |
| Play Store | On the phone | In the car, **only with GAS** |
| OEM customization | Limited (Google-defined UI) | Extensive |
| Updates | Via phone app updates | Vehicle OTA (system + apps) |
| Source code | Proprietary (Google app + receiver SDK) | **Open source** platform (AOSP), GAS proprietary |
| Primary purpose | Smartphone projection | Native vehicle OS |

---

## 5. AAOS in production vehicles

> **External (market) information, checked November 2026.** Model availability varies by **market,
> model year and trim**. Always confirm with the OEM or Google's "Google built-in" pages.

### 5.1 Two deployment models

**Model 1: AAOS + GAS ("Google built-in").** Typical features: Google Maps, Google Assistant or
Gemini, Play Store, Play services. Users get a familiar Google experience, and the OEM signs a Google
agreement.

**Model 2: AAOS without GAS.** The OEM provides its own (or partners') navigation, voice assistant,
app store and cloud services, and keeps full control of branding, data and regional providers.

```text
   Model 1                         Model 2
 Android Automotive OS          Android Automotive OS
          │                              │
 Google Automotive Services      OEM / partner services
  Maps · Assistant/Gemini         navigation (e.g. HERE, TomTom)
  Play Store · Play services      voice (e.g. Cerence, Alexa)
                                  app store (e.g. third-party store)
```

### 5.2 Volvo Cars

Among the first adopters of AAOS **with GAS**. Google built-in first appeared in the **Volvo XC40
Recharge** and **Polestar 2**, both launched in the second half of **2020** (External). Volvo now
offers it across many models (XC40, XC60, XC90, S60, S90, EX30, EX90 and others), with climate,
vehicle settings, navigation and media in one UI, and regular OTA updates.

### 5.3 Polestar

Polestar was created as Volvo's performance brand and became a standalone EV maker within the Geely
group. The **Polestar 2** was the **first production car with Google built-in** (2020, External).
Polestar remains a close partner and often demonstrates new AAOS capabilities. Recent reporting
mentions **Gemini** coming to its vehicles.

> **Correction:** the previous version of chapter 02 said "Volvo was the first to implement AAOS in
> 2019". Production vehicles with Google built-in shipped in **2020**. The first was the **Polestar 2**,
> closely followed by the Volvo XC40 Recharge.

### 5.4 Other OEMs using Google built-in (External)

- **General Motors:** Google built-in in many **model year 2022+** Chevrolet, GMC, Cadillac and
  Buick vehicles, branded per brand.
- **Renault Group:** Mégane E-Tech, Austral, Scénic E-Tech and others, branded **OpenR Link**.
- **Honda:** starting with the **Accord** and the **Prologue**.
- **Ford / Lincoln:** announced Google apps and services in future vehicles from 2023. Rollout scope
  has been described as limited so far.
- **Geely group:** extending Google built-in from Volvo and Polestar to more group brands.
- Others such as **Nissan** and **Mitsubishi** in some models and markets.

### 5.5 AAOS without GAS: examples

- **BMW:** **BMW Operating System 9** is built on **AOSP** (BMW press release). Its "Shared Service
  Layer" stays Linux-based, it does **not** use Google services, and its app store is provided by
  **Aptoide** (External).
- **Lucid Air:** reported to use AAOS **without** GAS (External).
- Reporting also lists **Audi, Porsche, Stellantis, Mini** and others among brands using AAOS-based
  systems without the full GAS package, or with only some Google apps (External; varies by model and
  market).
- **China and other regions where Google services are unavailable:** OEMs ship AAOS (or other
  Android-based systems) with local maps, voice and stores (General concept).

> **Correction:** the previous version listed Audi, Porsche and Lucid under "manufacturers using AAOS"
> together with GAS adopters. Current reporting places them in the **without-GAS** (or partial) group.

### 5.6 Why OEMs choose differently

| Consideration | AAOS + GAS | AAOS without GAS |
|---|---|---|
| Google Maps | ✓ | Alternative (HERE, TomTom, own) |
| Voice assistant | Google Assistant / Gemini | Alternative (Cerence, Alexa, own) |
| App store | Google Play | OEM/third-party store |
| Google Play services | ✓ | Not included |
| User experience | Familiar Google ecosystem | Fully OEM-defined |
| Licensing | Google agreement + compatibility requirements | No Google licensing for services |
| Data and branding control | Shared with Google services | Maximum OEM control |
| Regional fit | Not where Google services are unavailable | Adaptable to any region |

Neither is inherently better. The choice depends on strategy, markets, customer expectations,
existing software investment and partnerships.

---

## 6. Common misconceptions

| Misconception | Correct interpretation |
|---|---|
| "AAOS and Android Auto are the same." | Android Auto = **projection from a phone**. AAOS = **the car's own OS**. |
| "AAOS = Google Maps + Play Store in the car." | Those come from **GAS**, an optional licensed layer. AAOS itself is open source. |
| "AAOS automotive code is proprietary." | CarService, the Car API, the VHAL interface, EVS and the car apps are in **AOSP** (`packages/services/Car`, `hardware/interfaces/automotive`, `packages/apps/Car`). |
| "Apps in AAOS can control the car freely." | Every vehicle access goes through CarService permission checks, and SELinux blocks direct VHAL access ([AAOS Architecture §7](../06.AndroidAutomotiveArchitecture/README.md#7-security-permissions-and-selinux)). |
| "You build AAOS with Gradle." | Apps use Gradle. The **platform** is built with Soong/Make from AOSP. |
| "An AAOS car cannot use Android Auto." | It can, if the OEM integrates the Android Auto receiver. They are independent technologies. |

**Sources (external facts):**
[Android Automotive (Wikipedia overview)](https://en.wikipedia.org/wiki/Android_Automotive),
[Cars with Google built-in (Android Headlines)](https://www.androidheadlines.com/android-automotive),
[Geely extends Google built-in](https://eletric-vehicles.com/geely/geely-extends-google-built-in-from-volvo-and-polestar-to-more-group-brands/),
[BMW Group press release: BMW OS 9 on AOSP](https://www.press.bmwgroup.com/global/article/detail/T0401875EN),
[BMW OS 9 without Google services (9to5Google)](https://9to5google.com/2023/01/06/bmw-android-automotive-2/).
Official: [source.android.com/docs/automotive](https://source.android.com/docs/automotive).
