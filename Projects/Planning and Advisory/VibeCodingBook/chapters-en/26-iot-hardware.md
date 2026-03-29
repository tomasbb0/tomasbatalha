# Chapter 26 Beyond the Screen: IoT, Hardware & the Physical World

> "The Analytical Engine weaves algebraical patterns, just as the Jacquard loom weaves flowers and leaves."
> (Ada Lovelace, 1843)

Everything in this book so far lives on a screen. Websites, extensions, dashboards, APIs. But vibe coding does not stop at the browser. In December 2025, a team at Adafruit prototyped an AI-powered companion robot in three days: it talks, sees, reacts with an expressive E-ink face, plays audio through dual speakers, and runs an on-device AI pipeline on a Raspberry Pi 5. They built it the same way you have been building throughout this book. Small loops, constant feedback, AI-assisted code.

If code can control a browser, it can control a motor. If it can fetch an API, it can read a temperature sensor. The physical world is just another interface.

## Key Terms

| Term                      | What it means                                                                              |
| ------------------------- | ------------------------------------------------------------------------------------------ |
| **IoT**                   | Internet of Things: physical devices connected to the internet that collect or act on data |
| **Microcontroller**       | A small, cheap computer on a single chip (Arduino, ESP32) that runs one program in a loop  |
| **Single-Board Computer** | A full computer the size of a credit card (Raspberry Pi) that runs an operating system     |
| **GPIO**                  | General Purpose Input/Output: the pins on a board that connect to sensors and actuators    |
| **Firmware**              | The code that runs directly on a microcontroller (typically C/C++ or MicroPython)          |
| **Actuator**              | A component that does something physical: motor, LED, buzzer, servo                        |
| **Sensor**                | A component that reads something physical: temperature, light, motion, distance            |
| **MQTT**                  | A lightweight messaging protocol designed for IoT devices                                  |
| **Edge computing**        | Processing data on the device itself instead of sending everything to the cloud            |

## Why It Matters

Software eats the world, but IoT is software that touches the world. A web app displays data. An IoT device controls your heating, monitors your plants, locks your door, tracks your inventory, or drives a robot. The market for IoT developers is enormous and growing. More importantly: the barrier to entry has collapsed.

In 2020, building a connected device required electrical engineering knowledge, embedded C programming, and months of development. In 2026, you can describe what you want to Claude, get working Arduino code, upload it to a board, and see an LED blink, in under ten minutes. The same AI that writes your React components writes your firmware.

## The Two Worlds of IoT Hardware

### Microcontrollers: Arduino & ESP32

A microcontroller is a tiny, cheap computer (€3 to €15) that runs one program on repeat. No operating system, no file system, no browser. Just your code and the hardware.

**Arduino Uno.** The original. Simple, massive community, mountains of tutorials. Runs C/C++. Perfect for learning. Limited processing power and no Wi-Fi.

**ESP32.** The current favourite. Wi-Fi and Bluetooth built in, powerful dual-core processor, costs about €5. Runs Arduino code (C/C++) or MicroPython. This is the board you want for most IoT projects.

**What they are good for:** Reading sensors, controlling motors, blinking LEDs, sending data to the cloud, running simple automations. Low power consumption means they can run on batteries for months.

### Single-Board Computers: Raspberry Pi

A Raspberry Pi is a full Linux computer the size of a credit card (€35 to €80). It runs an operating system (Raspberry Pi OS, based on Debian), has USB ports, HDMI output, GPIO pins, and Wi-Fi.

**What it is good for:** Running Python scripts, hosting a local web server, processing camera feeds, running on-device AI models, acting as a hub for multiple sensors. When you need more processing power than a microcontroller provides (image recognition, natural language processing, complex decision-making), Raspberry Pi is the answer.

The Adafruit companion robot used a Raspberry Pi 5 precisely because it needed to run an AI pipeline: camera → vision model → decision → speech synthesis → speaker output. A microcontroller cannot do that.

## Vibe Coding Hardware: The Workflow

The workflow is almost identical to what you have been doing throughout this book. The differences are small.

### Step 1: Describe what you want

```
Prompt to Claude or ChatGPT:

"I have an ESP32 board and a DHT22 temperature sensor connected to GPIO pin 4.
Write Arduino code that reads the temperature every 30 seconds and sends it
to a web endpoint via HTTP POST as JSON. Include Wi-Fi connection setup
with SSID and password as variables at the top."
```

### Step 2: Get working code

The AI generates complete, compilable Arduino code. Sensor reading, Wi-Fi connection, HTTP request. All included. You copy it into the Arduino IDE (free, works like a simplified VS Code) or PlatformIO (a VS Code extension for embedded development).

### Step 3: Upload and test

Click "Upload" in the Arduino IDE. The code compiles and transfers to your board via USB cable. Open the Serial Monitor to see output. The temperature appears. The HTTP request fires. Your physical device is now talking to the internet.

### Step 4: Iterate

"The temperature is reading 0. I think the sensor pin is wrong." Paste the error into the AI chat. It fixes the pin assignment. Upload again. Working.

This is vibe coding. The loop is the same: describe → generate → test → fix. The only difference is that "test" means looking at a real sensor reading instead of a web page.

The same loop works for Chrome extensions, web apps, mobile apps, and now hardware. JobPilot follows the exact same cycle: describe what the content script should do → generate → test on a live Greenhouse page → fix. The runtime is different (a browser tab instead of a microcontroller), but the workflow is identical. If you've been building JobPilot through this book, you already know the loop. Apply it to anything.

If you want to build something that connects to physical devices (a smart home controller, a weather station, a plant watering system), the vibe coding process is the same. Only the hardware changes.

## Project: A Smart Environment Monitor

Here is a complete project you can build in one afternoon, using only vibe coding and a €15 ESP32 kit.

**What it does:** Reads temperature and humidity every minute, sends data to a free cloud dashboard, alerts you on your phone if the temperature exceeds a threshold.

**Hardware needed:**

- ESP32 development board (€5)
- DHT22 temperature/humidity sensor (€3)
- 3 jumper wires (included in most kits)
- USB-C cable (you already have one)

**Software stack (all free):**

- Arduino IDE or PlatformIO (VS Code extension)
- ThingSpeak (free IoT cloud dashboard, up to 8 channels)
- IFTTT (free tier: get phone notifications when temperature exceeds threshold)

### The firmware (AI-generated, human-reviewed)

```cpp
#include <WiFi.h>
#include <HTTPClient.h>
#include <DHT.h>

// Configuration — change these
const char* WIFI_SSID = "your-network";
const char* WIFI_PASS = "your-password";
const char* THINGSPEAK_API_KEY = "your-api-key";

// Hardware
#define DHT_PIN 4
#define DHT_TYPE DHT22
DHT dht(DHT_PIN, DHT_TYPE);

void setup() {
  Serial.begin(115200);
  dht.begin();

  // Connect to Wi-Fi
  WiFi.begin(WIFI_SSID, WIFI_PASS);
  Serial.print("Connecting to Wi-Fi");
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println(" Connected!");
}

void loop() {
  float temperature = dht.readTemperature();
  float humidity = dht.readHumidity();

  if (isnan(temperature) || isnan(humidity)) {
    Serial.println("Failed to read sensor!");
    delay(5000);
    return;
  }

  Serial.printf("Temp: %.1f°C  Humidity: %.1f%%\n", temperature, humidity);

  // Send to ThingSpeak
  if (WiFi.status() == WL_CONNECTED) {
    HTTPClient http;
    String url = "http://api.thingspeak.com/update?api_key="
                 + String(THINGSPEAK_API_KEY)
                 + "&field1=" + String(temperature)
                 + "&field2=" + String(humidity);
    http.begin(url);
    int httpCode = http.GET();
    Serial.printf("ThingSpeak response: %d\n", httpCode);
    http.end();
  }

  delay(60000); // Wait 1 minute
}
```

Every line of this was generated by AI. You review it, understand the structure (setup runs once, loop runs forever, sensor data goes to cloud via HTTP), and upload.

### The dashboard

ThingSpeak automatically creates graphs from your data. Within an hour, you have a live dashboard showing temperature and humidity over time. Accessible from any browser. Free.

### The alert

IFTTT's free tier: "If ThingSpeak field1 > 30, then send me a push notification." Set up in 2 minutes. Now your phone buzzes when your room gets too hot.

**Total cost: €8 hardware + €0 software. Time: one afternoon. Lines of code you wrote by hand: zero.**

## Vibe Coding Robots

A robot is just an IoT device that moves. A servo motor on an ESP32, controlled by code that reacts to sensor input (or AI decisions), is a robot.

### The simplest robot: a servo that follows light

Two photoresistors (€0.50 each) and one servo motor (€3). Ask the AI:

```
"Write Arduino code for an ESP32 with two LDR photoresistors on analog pins
34 and 35, and a servo on GPIO pin 13. The servo should turn toward whichever
photoresistor detects more light. Smooth the movement with a simple average
of the last 5 readings."
```

The AI generates the entire program: analog reads, averaging, servo angle mapping. Upload. Point a flashlight at one sensor. The servo turns. That is a light-tracking robot, built with vibe coding.

### The ambitious robot: AI companion on Raspberry Pi

Following the Adafruit pattern, a Raspberry Pi 5 with a camera module and a speaker can become an AI companion:

1. **Camera captures a frame** (Python + picamera2 library)
2. **Vision model describes what it sees** (Google Gemini API, free tier)
3. **Text-to-speech converts the description to audio** (pyttsx3, runs locally, free)
4. **Speaker plays the response**

The entire pipeline is Python. Every component is a library you install with pip. The AI writes each piece. You connect them in a main loop. The robot "sees" and "talks."

```python
import picamera2
import google.generativeai as genai
import pyttsx3
from PIL import Image
import io

# Setup
genai.configure(api_key="your-gemini-key")
model = genai.GenerativeModel("gemini-2.0-flash")
engine = pyttsx3.init()
camera = picamera2.Picamera2()
camera.start()

def see_and_speak():
    # Capture image
    frame = camera.capture_array()
    image = Image.fromarray(frame)

    # Ask Gemini what it sees
    response = model.generate_content([
        "Describe what you see in one short, friendly sentence.",
        image
    ])

    description = response.text
    print(f"I see: {description}")

    # Speak it
    engine.say(description)
    engine.runAndWait()

# Main loop
while True:
    see_and_speak()
```

Twenty-five lines of Python. The robot sees, thinks, and speaks. All libraries are free. The Gemini API free tier handles 1,500 requests per day, enough for a companion that responds every minute for over 24 hours.

## When AI-Generated Firmware Fails

Hardware has constraints that web code does not. AI-generated firmware can fail in ways that AI-generated websites cannot:

**Timing issues.** Microcontrollers run in real-time. If your code takes too long in one loop iteration, sensor readings get delayed, servos stutter, and communication drops. AI often generates code with blocking delays that cascade into bugs.

**Memory limits.** An ESP32 has 520 KB of RAM. An Arduino Uno has 2 KB. AI routinely generates code that allocates strings, arrays, and buffers without thinking about memory. On a web server, this causes slowness. On a microcontroller, it causes a crash.

**Power management.** A battery-powered sensor should sleep between readings. AI often forgets deep-sleep mode, generating code that keeps the processor awake (and drains the battery in hours instead of months).

**Pin conflicts.** AI sometimes assigns the same GPIO pin to two functions, or uses pins that are reserved for internal functions (like boot selection on ESP32 pins 0, 2, and 12).

**The fix is always the same:** describe the problem to the AI, include the error message or unexpected behaviour, and ask it to correct the specific issue. The prompt-run-fix loop that works for web code works for firmware too. You just need to know what "wrong" looks like. This chapter told you.

## IoT Architecture: Where Everything Connects

An IoT system has the same boxes as a web app, plus one new one:

```
┌──────────┐     ┌──────────┐     ┌──────────┐     ┌──────────┐
│  Device   │────▶│  Gateway  │────▶│  Cloud   │────▶│Dashboard │
│(ESP32/RPi)│     │ (MQTT/HTTP)│    │(ThingSpeak│    │(Web app) │
│           │◀────│           │◀────│ AWS IoT) │◀────│          │
└──────────┘     └──────────┘     └──────────┘     └──────────┘
   Sensors          Protocol         Storage          Display
   Actuators        Translation      Processing       Alerts
```

**Device** = your hardware (ESP32 + sensors). Reads data, controls actuators.
**Gateway** = the communication layer. MQTT for lightweight messaging, HTTP for simple REST calls.
**Cloud** = where data is stored and processed. ThingSpeak (free), AWS IoT Core (pay-per-message), Firebase (free tier).
**Dashboard** = the web app you already know how to build. Displays data, sends commands back to the device.

You already know how to build the cloud and dashboard parts from earlier chapters. This chapter adds the device and gateway layers.

## The Physical World Needs Different Testing

You cannot unit test a motor. You cannot mock a temperature sensor without knowing what "correct" looks like in the physical world.

**Testing strategy for IoT:**

1. **Serial Monitor first.** Print every sensor reading and every action to the Serial Monitor before connecting anything to the cloud. Make sure the device works in isolation.

2. **Simulate before connecting.** Use fixed values in your code before reading real sensors. If the logic works with `temperature = 25.0`, connect the real sensor.

3. **Test the network separately.** Connect to Wi-Fi and send a test HTTP request to a known endpoint (like httpbin.org) before connecting to your cloud service.

4. **Monitor power consumption.** If battery-powered, measure actual current draw. A USB multimeter (€10) tells you exactly how long your battery will last.

## Common Beginner Mistake

**Treating hardware like software.** In a web app, a bug shows an error message. In hardware, a bug can mean a motor that doesn't stop, a heater that stays on, or a sensor that reports false data that triggers real-world actions. Always add safety bounds: maximum motor runtime, temperature limits, watchdog timers that restart the device if it hangs. AI will not add these unless you ask.

## Checklist

- [ ] I chose the right board for my project (microcontroller for simple sensing, Raspberry Pi for AI/camera)
- [ ] I verified the wiring before uploading code (wrong wiring can damage components)
- [ ] I tested each component individually before combining them
- [ ] I added safety bounds for all actuators (max runtime, temperature limits)
- [ ] I handle Wi-Fi disconnection gracefully (retry logic, local data buffering)
- [ ] I reviewed the AI-generated code for memory issues (large arrays, string concatenation in loops)
- [ ] I set API keys as variables at the top, not hardcoded in function calls
- [ ] I have a way to update the firmware remotely (OTA) or plan to connect via USB

## Summary

Vibe coding extends beyond the screen. The same prompt-generate-test-fix loop that builds web apps builds IoT devices. Microcontrollers (ESP32, Arduino) run firmware generated by AI: sensor reading, Wi-Fi connection, cloud communication. Single-board computers (Raspberry Pi) run Python scripts for AI-powered devices: vision, speech, decision-making. Hardware introduces constraints that web code does not have: limited memory, real-time timing, power management, and physical safety. The architecture mirrors web apps (device, gateway, cloud, dashboard) and you already know how to build half of it. An environment monitor costs €8 in parts and one afternoon of vibe coding.

## Challenge

1. **Beginner (Mode 1):** Ask ChatGPT to generate Arduino code that blinks an LED on an ESP32 in a pattern that spells "SOS" in Morse code. Review the timing logic. If you have the board, upload and test. If not, trace the code and explain what each delay does.

2. **Intermediate (Mode 2):** Build the environment monitor project from this chapter. Set up ThingSpeak, wire the DHT22 to an ESP32, upload the AI-generated code, and verify data appears on the dashboard. Add an IFTTT alert for high temperature.

3. **Advanced (Mode 3):** Build a Raspberry Pi companion that uses the camera to detect when someone enters the room (using a free vision model) and greets them by name using facial recognition (OpenCV + a small training set). The AI pipeline: camera → face detection → recognition → text-to-speech greeting.
