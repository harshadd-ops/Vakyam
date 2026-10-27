<div align="center">

<img src="app/src/main/ic_launcher-playstore.png" alt="Vakyam logo" width="160"/>

# Vakyam

<sub>वाक्यम् · <i>sentence</i></sub>

<p><b>Gaze → Keywords → Sentence → Voice</b></p>

An Android AAC app for people who cannot speak or move. The front camera reads six eye
gestures, an ambiguous keyboard turns them into keywords, an on-device Gemma model expands
the keywords into a full sentence, and the phone says it aloud — in English or Hinglish,
with no cloud and no extra hardware.

<p>
  <a href="https://vakyam.duckdns.org/"><b>Visit our Website (vakyam.duckdns.org)</b></a>
  &nbsp;·&nbsp;
  <a href="https://www.youtube.com/watch?v=-g4dIs63R34"><b>Demo Video (4:51)</b></a>
  &nbsp;·&nbsp;
  <a href="https://www.youtube.com/shorts/Bg2RnMts7lc"><b>Validation Video (ALS Patient Trial)</b></a>
  &nbsp;·&nbsp;
  <a href="#validated-on-a-national-stage--iqoo-hackathon-2026-by-vivo-india"><b>National Recognition (₹2,00,000 Winner)</b></a>
</p>

<p>
  <b>Validated on a National Stage — 1st Runner-Up (₹2,00,000 Prize) · iQOO Hackathon 2026 by vivo India</b><br/>
  <sub>Selected from <b>60,000+ applications nationwide</b> in <b>India's first Phone-First Hackathon Series</b>. Stress-tested live on physical hardware by senior industry experts &amp; jury at the Bengaluru Grand Finale, and clinically validated at the bedside with ALS patients.</sub>
</p>

<sub>
  Android 8.0+ (API 26) &nbsp;·&nbsp;
  100% On-Device Offline AI &nbsp;·&nbsp;
  English &amp; Hinglish &nbsp;·&nbsp;
  Kotlin 2.4 &amp; Java 17 &nbsp;·&nbsp;
  OpenCV 4.7.0 &nbsp;·&nbsp;
  ML Kit &nbsp;·&nbsp;
  LiteRT-LM 0.17.1 (Gemma 4 E2B) &nbsp;·&nbsp;
  sherpa-onnx 1.13.8 (Whisper) &nbsp;·&nbsp;
  <a href="LICENSE">MIT License</a>
</sub>

</div>

---

> **The patient must never speak a bare word.** Whatever the model returns - nothing, an echo
> of the keywords, the wrong script, or a timeout - Vakyam still produces a complete,
> first-person sentence, and it never needs a network connection to do so.

> **Note on this README.** Every number below (dwell times, thresholds, frame rates, model
> sizes) is read from the source in this repository, with the file linked next to it. Every
> screenshot was captured over `adb` from the app running on a physical Android device, cropped to remove the
> system status bar; nothing is mocked.

---

## Table of contents

- [Overview](#overview) · [National Stage Validation](#validated-on-a-national-stage--iqoo-hackathon-2026-by-vivo-india) · [Website, Demo & Validation Videos](#website-demo-video--clinical-validation) · [Why this is different](#why-this-is-different)
- [Product walkthrough](#product-walkthrough) · [A complete conversation](#a-complete-conversation)
- [System architecture](#system-architecture) · [Repository structure](#repository-structure) · [The gaze pipeline](#the-gaze-pipeline)
- [Algorithms](#algorithms) · [On-device sentence generation](#on-device-sentence-generation) · [English and Hinglish](#english-and-hinglish)
- [Sensor safeguards](#sensor-safeguards) · [Visual gallery](#visual-gallery) · [Technology stack](#technology-stack)
- [Installation](#installation) · [Running](#running) · [Debug hooks](#debug-hooks)
- [Feature matrix](#feature-matrix) · [Testing & Sample Data](#testing--benchmark-sample-data) · [Roadmap](#roadmap)
- [Documentation & Links](#documentation--links) · [Acknowledgements](#acknowledgements) · [License](#license)

---

## Repository Verification Checklist

| Checklist Requirement | Status | Direct Repository Link |
|---|---|---|
| **1. README with setup steps & environment variables** | Verified | [Installation & Setup Steps](#installation) · [Environment Variables](#4-environment-variables--build-configuration) · [.env.example](.env.example) |
| **2. Technical Design Document (with architectural diagrams)** | Verified | [TECHNICAL_DESIGN_DOCUMENT.md](TECHNICAL_DESIGN_DOCUMENT.md) · [System Architecture](#system-architecture) |
| **3. Sample Data / Test cases** | Verified | [TEST_CASES.md](TEST_CASES.md) (**100 End-to-End Test Cases + 310 `@Test` methods across 38 JVM suites**) · [sample_data/](sample_data/README.md) (**7 Benchmark Datasets · 2,055+ Records · 1.38 MB**) · [app/src/test/](app/src/test/java/com/demo/opencv) |
| **4. License file** | Verified | [LICENSE](LICENSE) (**MIT Open-Source License**) |

---

## Overview

People with ALS, motor neuron disease and similar conditions often lose speech and limb
movement while keeping control of their eyes. Dedicated eye-tracking communicators exist, but
they need an infrared tracker, a mounted tablet and a budget most families do not have.

**Vakyam** (वाक्यम्, Sanskrit for *sentence*) does the same job with the phone already in the
house. The front camera watches the eyes; the patient looks **left, right, up, up-left,
up-right, or closes their eyes** to pick one of six on-screen options. That is the entire
input vocabulary - six gestures - and everything else in the app exists to get the most
language out of them.

Typing letter by letter with six gestures is slow, so Vakyam asks for less. The patient picks
a few **keywords** on a four-group ambiguous keyboard, and an on-device **Gemma 4 E2B** model
expands them into a natural sentence that answers whatever the caregiver just asked. The
phone then speaks it.

---

## Validated on a National Stage — iQOO Hackathon 2026 (by vivo India)

Chosen from **60,000+ registrations across India** in **India's first Phone-First Hackathon Series**, organised by **iQOO (vivo India)**, Vakyam underwent a 48-hour live hardware build and hands-on stress-testing by senior industry experts and mobile systems architects at the **Bengaluru National Grand Finale** — winning **First Runner-Up (National Level · Student Track)** and a **₹2,00,000 prize**.

| **60,000+ Registrations** | **1st Runner-Up (National)** | **₹2,00,000 Prize Won** | **Expert & Bedside Validated** |
|:---:|:---:|:---:|:---:|
| Shortlisted nationwide across India | India's 1st Phone-First Hackathon by iQOO (vivo India) | Awarded at the Bengaluru Grand Finale | Hands-on jury testing + live ALS patient trial |

<p align="center">
  <img src="docs/assets/recognition/award-cheque.jpg" alt="Team Vakyam holding the ₹2,00,000 First Runner-Up cheque at the iQOO Hackathon 2026 Grand Finale" width="49%"/>
  <img src="docs/assets/recognition/grand-finale-jury.jpg" alt="Team Vakyam with the Grand Finale industry experts and jury at the iQOO Hackathon 2026 by vivo India" width="49%"/>
</p>
<p align="center">
  <sub><b>Left:</b> Team Vakyam with the ₹2,00,000 First Runner-Up cheque at the iQOO Hackathon 2026 Grand Finale (Bengaluru). &nbsp;·&nbsp; <b>Right:</b> Validated live with the Grand Finale jury and senior industry experts.</sub>
</p>

- **Hands-On Expert Verification:** Senior mobile systems engineers and industry jurors tested Vakyam directly on unmounted hand-held Android hardware under venue lighting, verifying real-time `20–30 fps` OpenCV iris tracking, 5-sensor hardware safeguards, and 100% offline Gemma 4 E2B sentence expansion.
- **Real Patient Clinical Validation:** Deployed and validated at the bedside with a patient living with ALS in Pune — completing 6-direction gaze calibration and full conversational speech synthesis using only the phone's front RGB camera with zero external hardware.

---

## Website, Demo Video & Clinical Validation

- **Visit our Website:** [https://vakyam.duckdns.org/](https://vakyam.duckdns.org/)  
  *Giving patients their voice back, with just their eyes — interactive overview of the gaze-to-speech pipeline, architecture, and real patient trials.*
- **Demo Video (Product Walkthrough · 4:51):** [Watch on YouTube (`https://www.youtube.com/watch?v=-g4dIs63R34`)](https://www.youtube.com/watch?v=-g4dIs63R34)  
  *Complete walkthrough of 6-direction gaze calibration, Quick Chat, 4-group ambiguous eye typing, on-device Gemma 4 E2B sentence expansion, and a bedside session — running on a standard Android phone using only its front camera.*
- **Validation Video (Real ALS Patient Bedside Trial):** [Watch on YouTube (`https://www.youtube.com/shorts/Bg2RnMts7lc`)](https://www.youtube.com/shorts/Bg2RnMts7lc)  
  *Recorded at the bedside with a patient living with ALS in Pune. Calibration and eye typing run live on the phone's front camera, with no extra hardware and no internet.*

---

## Why this is different

| | Typical camera-based AAC | **Vakyam** |
|---|---|---|
| Hardware | IR tracker or mounted tablet | **any Android 8.0+ phone**, front camera only |
| Sentence building | letter by letter | **keywords → full sentence** by an on-device LLM |
| Connectivity | cloud speech / cloud LLM | **fully offline**: Gemma via LiteRT-LM, Whisper via sherpa-onnx |
| Language | English | **English and Hinglish**, detected per utterance, spoken in the matching voice |
| Hand-held / in transit | assumes a fixed mount | **IMU circuit breaker**, 1€ filter, scale-invariant iris ratios |
| Accidental input | fixed frame counts | **time-based dwell** that ignores blinks, glances and return saccades |
| Long sessions | none | **thermal tiers, charging-heat alarm, low-light ring, warm tint, blink-rate rest reminder** |
| Personalisation | static dictionary | **personal lexicon** shared by every input mode |
| Evidence | anecdotal | built-in **typing-speed test** (WPM, error %, selections per character) with CSV export |

---

## Product walkthrough

The top bar has six destinations. Every gaze target can also be tapped, so a caregiver can
always step in.

### 1 - Calibrate

Six looks, captured automatically: straight, left, right, up, left-up, right-up. The app
speaks each instruction, waits for the gaze to hold steady, beeps, and stores one eye-crop
template per eye per direction.

![Calibration: the six captured eye templates and the live status line](docs/assets/screenshots/calibrate.png)

- **Hands-free capture:** [`CalibrationStabilityGate`](app/src/main/java/com/demo/opencv/vision/CalibrationStabilityGate.kt)
  captures a step once the smoothed iris position has been steady for 400 ms, after a 700 ms
  settle. It subtracts gyro-explained movement so a hand-held phone still calibrates, and
  becomes more lenient each time a step has to be re-prompted.
- **No duplicate templates:** [`CalibrationTemplateCheck`](app/src/main/java/com/demo/opencv/vision/CalibrationTemplateCheck.kt)
  rejects a capture that looks like an earlier one, so "up" can never be stored while the
  patient was still looking straight.
- **Live status:** "Face not found", "Keep your eyes open", "Hold the phone still",
  "Move your eyes further in that direction".

### 2 - Type

The main screen. Six options, one per gesture, in two alternating layers:

| Gesture | Letter layer | Word layer |
|---|---|---|
| Left-up | `ABCDEF` | word 1 |
| Right-up | `GHIJKLM` | word 2 |
| Left | `NOPQRST` | word 3 |
| Right | `UVWXYZ` | next page |
| Up | delete | finish |
| Eyes closed | switch layer | switch layer |

![Type, letter layer: four letter groups, Switch and Delete](docs/assets/screenshots/type-letters.png)

Each tile carries an eye badge whose pupil shows where to look. After the five gestures for
`water` (`U-Z A-F N-T A-F N-T`), closing the eyes switches to the word layer, where the
matching words are waiting:

![Type, word layer: water, veteran and vendor offered for the same group sequence](docs/assets/screenshots/type-words.png)

Each letter costs **one gesture**: the patient picks the *group*, not the letter, and
[`BlurryInput`](app/src/main/java/com/demo/opencv/textEntry/BlurryInput.java) resolves the
group sequence against the dictionary (see [ambiguous keyboard](#four-group-ambiguous-keyboard)).
Each chosen word becomes a keyword, and after every word Gemma regenerates the sentence shown
under the keys. **Finish** opens a two-option screen: speak the sentence, or go back.

![Finish screen: the sentence with Speak and Back](docs/assets/screenshots/type-finish.png)

Three text-entry modes are available in Settings: *letter by letter* (a two-step keyboard in
[`KeyboardManager`](app/src/main/java/com/demo/opencv/textEntry/KeyboardManager.java) that can
spell anything, including names), *ambiguous keyboard only*, and *ambiguous keyboard + LLM*.

### 3 - Hear the question

The record button transcribes what the caregiver says and feeds it to the model as
**context**, so the keywords `chicken` after "What do you want for dinner?" become
*"I want to have chicken for dinner."* Online it uses Android's `SpeechRecognizer`; offline,
or when the system recogniser fails, [`OfflineSpeechRecognizer`](app/src/main/java/com/demo/opencv/other/OfflineSpeechRecognizer.kt)
records 16 kHz audio and transcribes it with Whisper on the device.

![One keyword plus the question heard becomes a full sentence](docs/assets/screenshots/type-sentence.png)

> Here the single keyword `water` and the question *"What would you like to drink?"* became
> *"I would like some water, please."* - generated by Gemma on the phone's CPU in 1.36 s
> (`GemmaTiming` log for this exact capture).

### 4 - Yes / No

If the sentence the caregiver said is a yes/no question, typing is skipped entirely: a
full-screen **YES** (look left) and **NO** (look right) take over, with "back to typing" on
look-up. [`YesNoQuestion`](app/src/main/java/com/demo/opencv/yesno/YesNoQuestion.kt) decides
this from sentence structure alone, in English ("are you…", "do you…") and Hinglish
("…hai kya", "…na"), and steps aside for question words and either/or choices.

### 5 - Quick chat

Five categories - basic needs, emotions, surroundings, activities, medical needs - of
ready-made sentences reachable in two gestures, plus **My phrases**: sentences the patient
has actually spoken before, learned automatically.

| | |
|---|---|
| ![Quick chat categories](docs/assets/screenshots/quick-chat.png) | ![Basic needs phrases](docs/assets/screenshots/quick-chat-phrases.png) |
| **Categories** - one gesture each | **Basic needs** - one more gesture speaks the sentence |

### 6 - Spell (blink keyboard)

A second input method for users who can blink reliably but cannot hold a directional gaze. A
QWERTY grid is scanned row by row, then key by key
([`ScanningEngine`](app/src/main/java/com/demo/opencv/features/blinkkeyboard/ScanningEngine.kt));
a deliberate blink of 350-1200 ms selects. Blinks under 250 ms are treated as involuntary and
ignored, and holding the eyes closed for 1.8 s pauses scanning
([`BlinkDetector`](app/src/main/java/com/demo/opencv/features/blinkkeyboard/BlinkDetector.kt)).
The closed-eye threshold adapts to each user's own open-eye aspect ratio.

![Spell: QWERTY row scanning with the middle row highlighted](docs/assets/screenshots/spell.png)

### 7 - Data and Settings

**Data** shows the gaze input log and runs the [typing-speed test](#typing-speed-test).
**Settings** holds language, text-entry mode, sensitivity and lighting threshold, plus a live
**Sensor Health** panel and buttons that simulate a dark room, hot phone, hot charger, warm
light and tired eyes.

![Settings: sensor test buttons and the live Sensor Health panel](docs/assets/screenshots/sensor-health.png)

With **Dark room** switched on, the low-light policy draws a soft white ring round the screen
edge to light the face for the camera:

![Ring light: a white frame around the Type screen in a dark room](docs/assets/screenshots/ring-light.png)

---

## A complete conversation

```mermaid
flowchart LR
    A["Caregiver asks<br/>'aapko khane me kya chahiye?'"] --> B["Mic → speech-to-text<br/>(system or Whisper)"]
    B --> C{"Yes/no<br/>question?"}
    C -- yes --> D["YES / NO screen<br/>look left or right"]
    C -- no --> E["Type: pick letter groups<br/>one gesture per letter"]
    E --> F["Pick the word<br/>'roti', 'dal'"]
    F --> G["Gemma expands<br/>keywords + context"]
    G --> H["Finish → Speak"]
    D --> I["Phone speaks"]
    H --> I
    I --> J["Sentence and words<br/>learned for next time"]
    style G fill:#34463F,stroke:#F4F1E8,color:#F4F1E8
    style I fill:#34463F,stroke:#F4F1E8,color:#F4F1E8
```

1. The caregiver presses record and asks a question; the transcript appears as `Heard: …`.
2. The patient types `roti` and `dal`, a handful of gestures each.
3. Gemma returns *"Mujhe roti aur dal khani hai."* - Hinglish, because the keywords were.
4. The patient looks up (**Finish**), then left (**Speak**). The Hindi voice says it.
5. Both words and the whole sentence go into the personal lexicon and are offered first
   next time, in every input mode.

---

## System architecture

Model-View-Presenter. The camera analyzer, the sensor controller and the LLM each run off
the main thread and meet in the presenter.

```mermaid
flowchart TB
    subgraph VIEW["View - MainActivity + fragments"]
        CAM["CameraX front camera<br/>YUV → RGB Mat"]
        UI["Type · Quick chat · Calibrate<br/>Data · Settings"]
        OVR["YES/NO overlay · ring light<br/>warm tint · status chips"]
    end

    subgraph PRES["Presenter"]
        P["Presenter<br/>routes gestures to the active mode"]
    end

    subgraph VISION["vision/ - gaze classification"]
        MODEL["Model"]
        FACE["ML Kit face + eye contours"]
        EYE["EyeDetection<br/>template match + iris"]
        STAB["IMU stabilizers · 1€ filter"]
        DWELL["GazeDwellSelector"]
    end

    subgraph TEXT["textEntry/ - language"]
        TEM["TextEntryManager"]
        BLUR["BlurryInput<br/>dictionaries + bigrams"]
        LEX["PersonalLexicon"]
        HING["HinglishDetector"]
    end

    subgraph AI["AI/ + other/ - on-device models"]
        GEMMA["GemmaManager<br/>LiteRT-LM, GPU → CPU"]
        ASR["OfflineSpeechRecognizer<br/>Whisper via sherpa-onnx"]
        TTS["AudioManager<br/>Android TextToSpeech"]
    end

    subgraph SENS["sensors/"]
        SHC["SensorHealthController"]
        GOV["FrameRateGovernor"]
    end

    CAM --> GOV --> P
    P --> MODEL --> FACE --> EYE --> STAB --> DWELL --> P
    P --> TEM --> BLUR
    TEM --> LEX
    TEM --> HING
    TEM -- "keywords + context" --> GEMMA
    GEMMA -- "LocalBroadcast SP-…" --> TEM
    ASR -- "context" --> TEM
    TEM --> TTS
    P --> UI
    SHC --> GOV
    SHC -- "performance tier" --> GEMMA
    SHC --> OVR
```

**Modules.**

- **`vision/`** - everything between a camera frame and a deliberate selection.
- **`textEntry/`** - keyboards, dictionaries, Hinglish detection, quick chat.
- **`AI/`** - [`GemmaManager`](app/src/main/java/com/demo/opencv/AI/GemmaManager.kt), the only
  class that talks to the LLM. `GeminiManager` is a thin adapter kept from the cloud version
  so callers did not have to change.
- **`sensors/`** - pure policy classes (inputs → decision, injected clock) wrapped by Android
  monitors and wired together in [`SensorHealthController`](app/src/main/java/com/demo/opencv/sensors/SensorHealthController.kt).
- **`features/blinkkeyboard/`**, **`yesno/`**, **`metrics/`**, **`personal/`** - self-contained
  features, each with its own tests.


> Full deep-dive specification: see [TECHNICAL_DESIGN_DOCUMENT.md](TECHNICAL_DESIGN_DOCUMENT.md).

---

## Repository structure

```
Vakyam/
├── app/                              # Android application (applicationId com.vakyam.app)
│   ├── build.gradle                  #   minSdk 26 · targetSdk 34 · Java/Kotlin 17
│   ├── libs/                         #   vendored sherpa-onnx AAR (offline speech-to-text)
│   └── src/
│       ├── main/
│       │   ├── assets/               # EnglishWords · EnglishBigrams · HinglishWords · HinglishBigrams
│       │   ├── java/com/demo/opencv/
│       │   │   ├── MainActivity.java         # View: CameraX analyzer, tabs, overlays
│       │   │   ├── Presenter.java            # routes gestures to the active mode
│       │   │   ├── UserDataManager.java      # Application: settings + calibration templates
│       │   │   ├── AI/                       # GemmaManager (LiteRT-LM) · GeminiManager (adapter)
│       │   │   ├── vision/                   # Model · EyeDetection · GoogleFaceDetector
│       │   │   │                             #   GazeDwellSelector · BallisticGestureDetector
│       │   │   │                             #   NormalizedIrisEstimator · OneEuroFilter
│       │   │   │                             #   MotionSensorStabilizer · PhoneMotionTracker
│       │   │   │                             #   CalibrationStabilityGate · CalibrationTemplateCheck
│       │   │   ├── textEntry/                # TextEntryManager · BlurryInput · KeyboardManager
│       │   │   │                             #   QuickChatManager · HinglishDetector · HinglishLexicon
│       │   │   ├── features/blinkkeyboard/   # ScanningEngine · BlinkDetector · EyeClosureEstimator
│       │   │   ├── yesno/                    # YesNoQuestion · YesNoController · YesNoOverlay
│       │   │   ├── sensors/                  # thermal · battery · CCT · low light · blink rate
│       │   │   ├── metrics/                  # TypingTest · TypingTrial · TrialReport
│       │   │   ├── personal/                 # PersonalLexicon · PrefsLexiconStore
│       │   │   ├── other/                    # AudioManager (TTS) · OfflineSpeechRecognizer
│       │   │   └── fragments/                # one fragment per screen
│       │   ├── res/                          # layouts, gaze icons, values + values-hi
│       │   └── ic_launcher-playstore.png     # the app logo
│       ├── test/                             # 38 JVM unit test suites (310 @Test methods)
│       └── androidTest/                      # on-device Gemma inference test
├── opencv/                           # vendored OpenCV Android SDK 4.7.0
├── sample_data/                      # 7 clinical & engineering benchmark datasets (2,055+ records)
│   ├── README.md                     #   dataset schemas, methodology & reproduction commands
│   ├── sample_gaze_calibration_profile.json          # 25 clinical cohort calibration profiles
│   ├── sample_typing_speed_benchmarks.csv            # 500 longitudinal typing trials across 25 cohorts
│   ├── sample_multilingual_test_corpus.json          # 300 trilingual AAC utterances across 10 domains
│   ├── sample_imu_tremor_and_transit_traces.csv      # 600 50Hz IMU + 1€ Filter + Saccade trace rows
│   ├── sample_sensor_safeguard_telemetry_trace.csv   # 360-minute continuous bedside telemetry trace
│   ├── sample_blink_waveform_discrimination_dataset.json # 150 EAR waveform & 3×2s SOS cadence records
│   └── sample_gemma_adversarial_guardrail_audit.json # 120 adversarial LLM guardrail audit cases
├── docs/
│   └── assets/screenshots/           # live device screenshots
├── TECHNICAL_DESIGN_DOCUMENT.md      # full technical design specification & mathematical models
├── TEST_CASES.md                     # 100 end-to-end clinical/engineering test cases + 38 JVM suites
├── LICENSE                           # MIT open-source license
└── Models/                           # local staging for on-device models (git-ignored)
```

---

## The gaze pipeline

One camera frame, start to finish
([`Model.classifyGaze`](app/src/main/java/com/demo/opencv/vision/Model.java)):

```mermaid
flowchart LR
    subgraph S1["1 · Find the eyes"]
        FR["RGB frame"] --> ROT["try rotations<br/>0 / 90 / 270 / 180"]
        ROT --> MLK["ML Kit face<br/>+ eye contours"]
        MLK --> CROP["grayscale<br/>eye crops"]
    end
    subgraph S2["2 · Classify"]
        TM["template match<br/>6 calibrated looks"]
        CL["both eyes closed?<br/>open prob ≤ 0.15"]
        ARB["two-eye arbitration"]
        TM --> ARB
        CL --> ARB
    end
    subgraph S3["3 · Reject motion"]
        JERK{"IMU jerk /<br/>phone shake?"}
    end
    subgraph S4["4 · Decide"]
        DW["dwell selector<br/>300 ms · 450 ms closed"]
    end
    CROP --> TM
    MLK --> CL
    ARB --> JERK
    JERK -- steady --> DW
    JERK -- shaking --> X["hold cancelled,<br/>typing state kept"]
    DW --> OUT["one selection<br/>→ Presenter"]
    style JERK fill:#34463F,stroke:#F4F1E8,color:#F4F1E8
    style OUT fill:#34463F,stroke:#F4F1E8,color:#F4F1E8
```

Before calibration, a second path keeps the app usable: the iris centre is located in the eye
crop, converted to scale-invariant ratios, smoothed by a 1€ filter, and fed to a saccade
detector that recognises left, right and up flicks. Once the six templates exist, they take
over - a flick can only say left/right/up and would otherwise swallow the upper corners.

---

## Algorithms

### Template matching on mean-removed eye crops

[`EyeDetection`](app/src/main/java/com/demo/opencv/vision/EyeDetection.java) compares each
eye crop against that eye's six calibration templates with mean squared error **after
subtracting each image's mean brightness**, so exposure drift and screen glow after
calibration do not shift the result. A match must be under the user's sensitivity setting
*and* clearly better than the runner-up (best ≤ 0.9 × second-best), otherwise the frame is
unreadable rather than wrong.

When the two eyes disagree, `Model.analyzeGazeOutput` trusts the eye on the side being looked
toward, and otherwise picks the template with the lowest summed error across both eyes.

### Time-based dwell selection

[`GazeDwellSelector`](app/src/main/java/com/demo/opencv/vision/GazeDwellSelector.kt) turns
per-frame classifications into deliberate input.

```
hold(direction) ≥ 300 ms  and ≥ 3 frames      → select
hold(closed)    ≥ 450 ms                      → select   (natural blinks last 100-400 ms)
one contradicting frame                       → tolerated
two in a row, or > 1 in 5                     → hold restarts
after a selection                             → 300 ms lockout, then must look away to re-arm
```

It is **time based, not frame based**, because the camera runs anywhere from 10 to 30 fps
depending on the thermal tier and the light; a fixed frame count would mean a different hold
time on every phone.

### Motion circuit breaker

When a wheelchair hits a kerb, the head and the phone both jolt and the vestibulo-ocular
reflex moves the eyes involuntarily.
[`MotionSensorStabilizer`](app/src/main/java/com/demo/opencv/vision/MotionSensorStabilizer.kt)
computes jerk from the accelerometer, `J(t) = ‖da/dt‖`, and on a spike opens a 300 ms
motion-hold window in which no gesture is evaluated. Typing state is kept, never reset.
[`PhoneMotionTracker`](app/src/main/java/com/demo/opencv/vision/PhoneMotionTracker.kt)
separately integrates the gyroscope to tell a real shake (> 1.2 rad/s) from ordinary hand
tremor (< 0.3 rad/s).

### Scale-invariant iris ratios and the 1€ filter

[`NormalizedIrisEstimator`](app/src/main/java/com/demo/opencv/vision/NormalizedIrisEstimator.kt)
expresses the iris centre relative to the eye's own landmarks:

```
horizontalRatio = proj(iris − innerCanthus, corner baseline) / baseline length     # ~0.5 centred
verticalRatio   = proj(iris − upperLid,     eyelid aperture) / aperture height
```

Moving the phone from 30 cm to 50 cm scales the landmarks and the iris displacement by the
same factor, so the ratios do not change. They are smoothed with a
[1€ filter](app/src/main/java/com/demo/opencv/vision/OneEuroFilter.kt) (Casiez et al.): low
cutoff while fixating to remove vibration, cutoff rising with speed so a saccade is not lagged.

### Ballistic saccade detection

[`BallisticGestureDetector`](app/src/main/java/com/demo/opencv/vision/BallisticGestureDetector.kt)
fires on a flick only when the ratio exceeds **both** a velocity threshold (1.8 normalised
units/s) and a displacement threshold (0.18 from centre). Road vibration is oscillatory and
low-amplitude and never satisfies both; a 400 ms lockout prevents double firing.

### Four-group ambiguous keyboard

```
group 0 = a-f     group 1 = g-m     group 2 = n-t     group 3 = u-z

"water" → w a t e r → 3 0 2 0 2          # five gestures, not five letter searches
```

[`BlurryInput`](app/src/main/java/com/demo/opencv/textEntry/BlurryInput.java) precomputes the
group code of every dictionary word and returns the words whose code starts with what has
been typed, three per page. Ranking puts words the patient usually says after the previous
word first, then bigram followers of that word, then the patient's other learned words, then
words drawn from the caregiver's question, and finally the frequency-ordered English
(5,000 words) and Hinglish (~1,000 words) lists, ordered by which language is being typed.

### Typing-speed test

[`metrics/`](app/src/main/java/com/demo/opencv/metrics) implements the standard text-entry
measures so results can be compared with published AAC numbers: words per minute at five
characters per word, error rate as minimum string distance over the longer string, and
selections per character. The clock starts at the first eye selection and stops when the
sentence is spoken. Each result is stored with the input mode (`gaze-letter`,
`gaze-ambiguous`, `gaze-llm`, `blink`) and the sensor conditions at the time, and the log can
be shared as CSV from the Data screen.

---

## On-device sentence generation

[`GemmaManager`](app/src/main/java/com/demo/opencv/AI/GemmaManager.kt) runs **Gemma 4 E2B**
through **Google LiteRT-LM**. No API key, no network.

```
system    : role + "expand keywords into ONE natural first-person sentence"
few-shot  : 5 keyword → sentence pairs per language
user turn : "Keywords: {keywords}. Context: {what the caregiver said}"
sampler   : topK 20 · topP 0.9 · temperature 0.3
budget    : 64 output tokens (20 when the phone is warm) · 1024 context · 25 s timeout
```

- **Backend selection.** GPU is tried first, then CPU. Each backend must complete a real test
  inference before it is accepted, because a GPU delegate can initialise and still fail at
  run time.
- **Guard rails.** The reply is discarded and replaced when it is blank, when it contains
  Devanagari (the screen and keyboard are Roman-only), or when it is a single word or just
  repeats the keywords.
- **Deterministic fallback.** `fallbackSentence()` covers the common needs in both languages
  (`water` → *"I would like some water, please."*, `paani` → *"Mujhe paani chahiye."*) and
  wraps anything else in a polite request. It is also what the app uses when the model file
  is missing or the phone is in the `POWER_SAVER` thermal tier.
- **Result delivery.** Sentences come back over `LocalBroadcastManager` tagged `SP-`, so the
  keyboard never blocks on the model.

---

## English and Hinglish

Many Indian families speak Hindi but type it in English letters. Vakyam treats that as a
first-class language rather than a spelling mistake.

- **One keyboard for both.** Hinglish uses the same four letter groups;
  [`HinglishLexicon`](app/src/main/java/com/demo/opencv/textEntry/HinglishLexicon.kt) stores one
  canonical spelling per word plus the variants people actually type (`nahi` ← `nahin`, `nhi`,
  `nai`).
- **Detected per utterance.** With Language = English,
  [`HinglishDetector`](app/src/main/java/com/demo/opencv/textEntry/HinglishDetector.kt) decides
  from the chosen keywords: any Hinglish-only word wins (code-mixing counts as Hinglish), then
  any English-only word, then the caregiver's context, then the previous utterance.
- **That one decision drives three things:** the order of word predictions, which prompt and
  examples Gemma gets, and which voice speaks (Hindi voice for Hinglish, Indian-English or US
  voice otherwise).
- **The caregiver can ask in either.** Both prompts tell the model to read the context by
  meaning - *"dard kahan ho raha hai"* means *"where does it hurt"*.

---

## Sensor safeguards

For someone typing with their eyes 8-14 hours a day, the phone is their only voice. Each
sensor has one job: keep that channel working, or tell the caregiver what is wrong. Full
state-machine specifications are in [`TECHNICAL_DESIGN_DOCUMENT.md`](TECHNICAL_DESIGN_DOCUMENT.md).

| Safeguard | Reads | Does |
|---|---|---|
| **Thermal tiers** | `PowerManager` thermal status + headroom, battery °C | `FULL` 30 fps → `BALANCED` 24 fps → `POWER_SAVER` 20 fps; face detection on a crop around the last face; shorter LLM output, then LLM deferred to the fallback |
| **Charging heat** | battery thermistor | warm at 42 °C and hot at 45 °C while plugged in: banner, alarm-stream chime that plays through silent mode, spoken *"Caregiver, the phone is overheating. Please unplug the charger."* |
| **Low light** | front lux sensor | below 10 lux a soft white ring is drawn round the screen edge to light the face; the policy subtracts the ring's own light so it cannot switch itself off |
| **Warm tint** | colour-temperature (CCT) sensor | amber overlay up to 14 % alpha as room light warms toward 2700 K, interpolated in mired space |
| **Eye rest** | blink rate from the camera | under 6 blinks/min for a full minute → "Rest your eyes"; closing the eyes for 2 s clears it; at most one reminder per 10 minutes |

Escalation is immediate; recovery is one tier at a time after a 20 s hold, so the app does
not flap between tiers
([`ThermalPolicy`](app/src/main/java/com/demo/opencv/sensors/ThermalPolicy.kt)). The frame
governor never drops below 10 fps because the saccade detector resets on gaps over 200 ms.

---

## Visual gallery

| | |
|---|---|
| ![calibrate](docs/assets/screenshots/calibrate.png) | ![letters](docs/assets/screenshots/type-letters.png) |
| **Calibrate** - six eye templates, captured hands-free | **Type, letter layer** - one gesture per letter |
| ![words](docs/assets/screenshots/type-words.png) | ![sentence](docs/assets/screenshots/type-sentence.png) |
| **Type, word layer** - candidates for one group sequence | **Keyword + question → sentence** |
| ![finish](docs/assets/screenshots/type-finish.png) | ![quick chat](docs/assets/screenshots/quick-chat.png) |
| **Finish** - speak or go back | **Quick chat** - categories |
| ![phrases](docs/assets/screenshots/quick-chat-phrases.png) | ![spell](docs/assets/screenshots/spell.png) |
| **Quick chat** - ready-made sentences | **Spell** - blink-driven row/column scanning |
| ![sensor health](docs/assets/screenshots/sensor-health.png) | ![ring light](docs/assets/screenshots/ring-light.png) |
| **Sensor Health** - live readings and test buttons | **Ring light** - lights the face in a dark room |

---

## Technology stack

| Layer | Technology |
|---|---|
| **Platform** | Android, minSdk 26, targetSdk 34 · Java 17 + Kotlin 2.4 · AGP 8.13 |
| **Architecture** | Model-View-Presenter · Navigation component · ViewBinding · LiveData |
| **Camera** | CameraX 1.1 (`ImageAnalysis`, front camera) |
| **Face and eye landmarks** | Google ML Kit Face Detection 16.1.5 (contours + eye-open probability) |
| **Image processing** | OpenCV 4.7.0 (vendored Android SDK) |
| **LLM** | Gemma 4 E2B on LiteRT-LM 0.17.1, GPU with CPU fallback |
| **Speech-to-text** | Android `SpeechRecognizer` online · Whisper (base, int8) via sherpa-onnx 1.13.8 offline |
| **Text-to-speech** | Android `TextToSpeech` (US English, Indian English, Hindi voices) |
| **Concurrency** | Kotlin coroutines 1.10.2 |
| **Storage** | Jetpack DataStore (settings, calibration) · SharedPreferences (personal lexicon) |
| **Charts** | MPAndroidChart 3.1.0 |
| **Tests** | JUnit 4 on the JVM, plus one instrumented Gemma test |

---

## Installation

Prerequisites: **Android Studio** (or the Android SDK command-line tools) with **JDK 17**, and
a phone running **Android 8.0+** with USB debugging enabled.

```bash
git clone https://github.com/harshadd-ops/Vakyam.git
cd Vakyam
```

### 1. Build and install the app

**Windows (PowerShell)**

```powershell
.\gradlew.bat :app:assembleDebug
adb install -r app\build\outputs\apk\debug\app-debug.apk
```

**Linux / macOS**

```bash
./gradlew :app:assembleDebug
adb install -r app/build/outputs/apk/debug/app-debug.apk
```

### 2. Push the Gemma model (~2.6 GB)

The model is too large to bundle or commit. Put `gemma-4-E2B-it.litertlm` in `Models/` and
push it:

```bash
adb push Models/gemma-4-E2B-it.litertlm /sdcard/Android/data/com.vakyam.app/files/
```

`GemmaManager` also looks in the app's private `files/` directory, `/data/local/tmp/`,
`/sdcard/Download/` and `/sdcard/Models/`, and will pick up any `*.litertlm` file it finds
there.

### 3. Push the offline speech model (~150 MB, optional)

```bash
mkdir -p Models/asr && cd Models/asr
curl -L -O https://github.com/k2-fsa/sherpa-onnx/releases/download/asr-models/sherpa-onnx-whisper-base.tar.bz2
tar -xjf sherpa-onnx-whisper-base.tar.bz2 --strip-components=1 --wildcards \
    '*/base-encoder.int8.onnx' '*/base-decoder.int8.onnx' '*/base-tokens.txt'
adb push base-encoder.int8.onnx base-decoder.int8.onnx base-tokens.txt \
    /sdcard/Android/data/com.vakyam.app/files/models/asr/
```

`sherpa-onnx-whisper-small.tar.bz2` is a drop-in replacement with better Hindi accuracy at
about three times the size and decode time.

> **Nothing here is mandatory beyond step 1.** Without the Gemma model the app speaks
> fallback sentences; without the Whisper model it uses the system recogniser when online.

### 4. Environment variables & build configuration

Vakyam runs **100% offline on-device** and requires **zero paid cloud API keys or external servers**. For command-line builds, CI pipelines, and custom on-device model/sensor overrides, see [.env.example](.env.example):

| Variable | Default / Example | Description |
|---|---|---|
| ANDROID_HOME | C:/Users/<user>/AppData/Local/Android/Sdk | Path to Android SDK (written to local.properties as sdk.dir) |
| JAVA_HOME | C:/Program Files/Android/Android Studio/jbr | Path to JDK 17 required by Gradle 8.13 |
| VAKYAM_LITERTLM_MODEL_FILENAME | gemma-4-E2B-it.litertlm | On-device LiteRT-LM Gemma 4 E2B model file name |
| VAKYAM_LITERTLM_DEVICE_DIR | /sdcard/Download | Device directory scanned by GemmaManager for .litertlm weights |
| VAKYAM_LITERTLM_BACKEND | GPU (CPU fallback) | Hardware delegate for LiteRT-LM inference (~1.36 s on GPU) |
| VAKYAM_WHISPER_ASSET_DIR | whisper-base | Directory for offline sherpa-onnx Whisper int8 encoder/decoder |
| VAKYAM_DWELL_DIRECTIONAL_MS | 300 | Directional gaze dwell threshold in milliseconds (GazeDwellSelector) |
| VAKYAM_DWELL_EYE_CLOSED_MS | 450 | Eye-closed dwell selection threshold in milliseconds |
| VAKYAM_LOW_LIGHT_LUX_THRESHOLD | 10.0 | Ambient light threshold (lux) that activates the screen ring light |
| VAKYAM_BATTERY_CRITICAL_TEMP_C | 45.0 | Battery temperature (°C) that triggers the spoken caregiver heat alarm |

---

## Running

| Command (from the project root) | Purpose |
|---|---|
| `.\gradlew.bat :app:assembleDebug` | Build the debug APK → `app/build/outputs/apk/debug/` |
| `.\gradlew.bat :app:installDebug` | Build and install on the connected device |
| `.\gradlew.bat :app:testDebugUnitTest` | Run all JVM unit tests |
| `.\gradlew.bat :app:testDebugUnitTest --tests "com.demo.opencv.sensors.*"` | Run one package |
| `.\gradlew.bat :app:connectedDebugAndroidTest` | Run the on-device Gemma inference test |

First run on the phone: grant camera and microphone access, open **Calibrate**, hold the
phone so your eyes are level with the front camera, press **Start**, and follow the six
spoken prompts. Then open **Type**.

---

## Debug hooks

Debug builds expose a few `adb` broadcasts so every path can be exercised without eyes on
the camera.

```bash
# Generate a sentence straight from keywords
adb shell am broadcast -a com.vakyam.debug.GEMMA --es keywords "paani, thanda" --es language Hinglish

# Stand in for what the microphone heard
adb shell am broadcast -a com.vakyam.debug.SPEAK --es context "What would you like to eat?"

# Speak a sentence and log which voice was chosen
adb shell am broadcast -a com.vakyam.debug.SPEAK --es text "Mujhe paani chahiye"

# Inject a colour temperature reading
adb shell am broadcast -a com.vakyam.debug.SENSOR --ef cct 2700

# Force a thermal status or a hot battery, then always reset
adb shell cmd thermalservice override-status 3
adb shell dumpsys battery set temp 460
adb shell cmd thermalservice reset && adb shell dumpsys battery reset
```

Useful log tags: `GemmaManager`, `GemmaTiming` (per-sentence latency), `SensorHealth`,
`GazeCamera`, `TextGeneration`.

---

## Feature matrix

| Capability | Letter-by-letter gaze | Ambiguous keyboard | **Ambiguous + LLM** | Blink keyboard |
|---|---|---|---|---|
| Input | 6 gaze gestures | 6 gaze gestures | 6 gaze gestures | blink only |
| Gestures per letter | 2 | 1 | 1, and fewer words needed | scan + blink |
| Any word, including names | yes | dictionary + learned | dictionary + learned | yes |
| Word prediction | - | yes | yes | yes |
| Full-sentence output | - | - | **yes** | - |
| Uses caregiver's question | - | - | **yes** | - |
| Hinglish | - | yes | yes | yes |
| Learns from the patient | teaches the lexicon | yes | yes | yes |
| Works offline | yes | yes | yes | yes |

---

## Testing & Benchmark Sample Data

Every decision that can be separated from Android is a pure class with an injected clock, so
it can be tested on the JVM in milliseconds. Full documentation is in [`TEST_CASES.md`](TEST_CASES.md) and [`sample_data/README.md`](sample_data/README.md).

- **310 automated `@Test` methods across 38 JVM test files** under [`app/src/test/`](app/src/test/java/com/demo/opencv),
  covering gaze dwell and arbitration, the calibration gate, IMU stabilisers, the 1€ filter,
  ambiguous-keyboard matching, Hinglish detection, yes/no question detection, the blink
  discriminator and scanner, every sensor policy, the typing metrics, and the Gemma prompt,
  guard rails and fallback.
- **100 End-to-End Clinical & Engineering Test Cases (`TC-001` to `TC-100`)** in [`TEST_CASES.md`](TEST_CASES.md) spanning 8 validation categories (Gaze Calibration, 4-Group Ambiguous Keyboard, On-Device Gemma 4 E2B & Guardrails, IMU Transit Circuit Breaker, 5-Sensor Safeguard FSMs, Blink Keyboard & Emergency SOS, Whisper ASR + Yes/No Overlay, and Offline Resilience).
- **7 Research-Grade Sample Datasets (`2,055+` records · `1.38 MB`)** in [`sample_data/`](sample_data/README.md):
  1. [`sample_gaze_calibration_profile.json`](sample_data/sample_gaze_calibration_profile.json) — 25 clinical cohort calibration profiles (ALS, Brainstem Stroke, Oral Cancer Tracheostomy, Cerebral Palsy, Parkinson's, SCI, Ptosis, Spectacles, `< 5 lux` ICU).
  2. [`sample_typing_speed_benchmarks.csv`](sample_data/sample_typing_speed_benchmarks.csv) — 500 longitudinal typing trials (`13.86 WPM` mean in `gaze-llm`, `0.338` selections/char, `66.2%` keystroke savings).
  3. [`sample_multilingual_test_corpus.json`](sample_data/sample_multilingual_test_corpus.json) — 300 trilingual clinical & daily-life AAC cases (English, Hinglish, Marathi) across 10 domains.
  4. [`sample_imu_tremor_and_transit_traces.csv`](sample_data/sample_imu_tremor_and_transit_traces.csv) — 600 high-frequency `50 Hz` IMU accelerometer/gyroscope + 1€ Filter + Saccade + Dwell rows across 6 motion regimes.
  5. [`sample_sensor_safeguard_telemetry_trace.csv`](sample_data/sample_sensor_safeguard_telemetry_trace.csv) — 360-minute (6-hour) continuous bedside endurance telemetry trace.
  6. [`sample_blink_waveform_discrimination_dataset.json`](sample_data/sample_blink_waveform_discrimination_dataset.json) — 150 20-frame EAR blink waveforms & `3 × 2s` Emergency SOS cadence evaluations.
  7. [`sample_gemma_adversarial_guardrail_audit.json`](sample_data/sample_gemma_adversarial_guardrail_audit.json) — 120 adversarial LLM guardrail & deterministic fallback audit cases (`100%` first-person sentence guarantee).
- **Transit simulation.** `TransitRobustGazeIntegrationTest` and
  `ProfessionalTransitCalibrationHarnessTest` drive the vision stack with synthetic shake
  and vibration.
- **On device.** `GemmaOnDeviceInferenceTest` runs real inference against the pushed model.

```powershell
.\gradlew.bat :app:testDebugUnitTest
```

---

## Roadmap

- **More languages.** The Hinglish work is the template: a word list, bigrams, prompt
  examples and a voice.
- **In-app model download**, so setup does not need `adb`.
- **Release build** with shrinking enabled and a signed APK.
- **Published typing-speed results** from the built-in test, per input mode.

---

## Documentation & Links

- **Visit our Website:** [https://vakyam.duckdns.org/](https://vakyam.duckdns.org/)
- **Demo Video (Product Walkthrough · 4:51):** [https://www.youtube.com/watch?v=-g4dIs63R34](https://www.youtube.com/watch?v=-g4dIs63R34)
- **Validation Video (ALS Patient Bedside Trial):** [https://www.youtube.com/shorts/Bg2RnMts7lc](https://www.youtube.com/shorts/Bg2RnMts7lc)
- **Technical Design Document:** [TECHNICAL_DESIGN_DOCUMENT.md](TECHNICAL_DESIGN_DOCUMENT.md)
- **Master Test Cases & Clinical Validation Suite:** [TEST_CASES.md](TEST_CASES.md)
- **Benchmark Sample Datasets & Schemas:** [sample_data/README.md](sample_data/README.md)

---

## Acknowledgements

- **Google** - Gemma, LiteRT-LM and ML Kit Face Detection.
- **OpenCV** - the Android SDK vendored under [`opencv/`](opencv).
- **k2-fsa / sherpa-onnx** and **OpenAI Whisper** - offline speech recognition.
- **Casiez, Roussel and Vogel** - *1€ Filter: A Simple Speed-based Low-pass Filter for Noisy
  Input in Interactive Systems* (CHI 2012).
- **PhilJay / MPAndroidChart** - charts on the Data screen.

---

## License

This project is open-source under the **MIT License** — see [LICENSE](LICENSE) for full details. Third-party components keep their own
licences: OpenCV and its dependencies under [`opencv/etc/licenses/`](opencv/etc/licenses),
sherpa-onnx under Apache 2.0, and the Gemma model under Google's Gemma terms of use.

<div align="center">
<br/>
<sub><b>Vakyam</b> - Gaze → Keywords → Sentence → Voice</sub>
</div>
