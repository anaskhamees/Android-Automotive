# Android-Automotive

**Android Automotive OS (AAOS): a source-code-backed learning reference**, from digital-cockpit
basics to AOSP platform internals, for students, AAOS platform engineers and interview preparation.

Every technical chapter is checked against real AOSP source code. Source paths, class and method
names, and line numbers refer to **AOSP `android-17.0.0_r1`** (Cuttlefish `aosp_cf_x86_64_auto`, whose system image is `car_generic`, and Raspberry
Pi 5 `aosp_rpi5_car` builds). Android 15 differences are called out where they exist.

## Learning path

Read in this order. Each chapter lists its prerequisites and links to the next.

| # | Chapter | What you learn |
|---|---|---|
| 00 | [Digital Cockpits](00.DigitalCockpits/README.md) | IVI, instrument cluster, HUD, cockpit domain controllers, and how AAOS models displays |
| 01 | [Linux vs Android](01.LinuxVsAndroid/README.md) | Kernel vs distribution, embedded Linux layers, what Android changes (IPC, memory, power, scheduling, security) |
| 02 | [Android History](02.AndroidHistory/README.md) | Releases, API levels, Android's path into the car, platform milestones |
| 03 | [Android Auto](03.AndroidAuto/README.md) | Projection: how phone and head unit connect (AOA handshake), app categories, limits |
| 04 | [Android Automotive OS](04.AndroidAutomotiveOS/README.md) | AOSP vs AAOS vs GAS, why AAOS exists, Android Auto vs AAOS, OEM deployment models |
| 05 | [Android Platform Architecture](05.AndroidArchitecture/README.md) | Layers vs processes, kernel features today, HALs and VINTF, native layer, ART, framework services, **Binder and ServiceManager** |
| 06 | [AAOS Architecture](06.AndroidAutomotiveArchitecture/README.md) | Car API → CarService → **Vehicle HAL** → vehicle. Vehicle properties, traced get/set/subscribe flows, permissions |
| 07 | [Boot Sequence](07.AndroidBootingSequence/README.md) | Boot ROM → bootloader (verified boot, with **[Secure Boot and AVB from zero](07.AndroidBootingSequence/SECURE_BOOT_AVB.md)**) → kernel → `init` → Zygote → `system_server` → CarService |
| 08 | [Build System](08.AndroidBuildSystem/README.md) | The AOSP source tree, product/board/module inputs, `lunch`, **Soong, Kati and Ninja**, `out/` and the images |
| 09 | [Build & Run AAOS](09.BuildAndRunAAOS/README.md) | `repo` and manifests, Cuttlefish and Trout, building and booting AAOS on Cuttlefish and on a **Raspberry Pi 5** |
| 10 | [Binder IPC and AIDL](10.AIDLHandsOn/README.md) | **One Binder call through every layer** (Proxy, Parcel, JNI, libbinder, kernel driver, Stub), then write AIDL services: app ↔ service, Java ↔ C++, and a **VINTF stable AIDL HAL** |

## How to read the evidence labels

| Label | Meaning |
|---|---|
| **Verified in source** | Read in the AOSP tree or build output; path and line given |
| **Official documentation** | source.android.com / developer.android.com |
| **General concept** | Standard Linux/automotive knowledge, not AOSP code |
| **Version-dependent** | Differs between Android releases |
| **Educational simplified example / Illustrative** | Teaching material, **not** real AOSP code |
