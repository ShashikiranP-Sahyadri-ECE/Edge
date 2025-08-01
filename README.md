
# TinyML with Edge Impulse and ESP32 IoT Development Trainer Kit by RDL Technologies Pvt.Ltd

This guide walks you through building a TinyML project using Edge Impulse and an ESP32 IoT Development Trainer Kit by RDL Technologies Pvt.Ltd with an LM35 temperature sensor. It covers setting up the software environment, writing the Arduino code, connecting the ESP32 to Edge Impulse, and training a simple ML model.

---

## 1. Prerequisites

Before getting started, make sure you have the following installed and ready:

- **Arduino IDE**  
  Used for writing and uploading code to your ESP32.

- **Python (v3.6 or above)**  
  Required for setting up tools and interacting with some Edge Impulse utilities.

- **Node.js (v20.x recommended)**  
  Required to install and use the Edge Impulse CLI tools.

- **Edge Impulse Account**  
  Create an account at [Edge Impulse Studio](https://studio.edgeimpulse.com).

---

## 2. Hardware Required

- ESP32 Development Board (ESP32 IoT Development Trainer Kit by RDL)
- LM35 Analog Temperature Sensor
- Jumper wires
- Breadboard
- Micro-USB Cable (for power and serial communication)

---

## 3. Arduino Sketch for LM35

Upload the following code using Arduino IDE:

```cpp
#define SENSOR_PIN 34

void setup() {
  Serial.begin(115200);
}

void loop() {
  int rawValue = analogRead(SENSOR_PIN);
  float voltage = rawValue * (3.3 / 4095.0);
  float temperature = voltage * 100.0; // LM35 => 10mV/°C

  Serial.println(temperature);
  delay(100);  // 10Hz
}


```
![0](https://github.com/user-attachments/assets/1e6ed020-50d3-4cca-9dfa-21390381f812)

### Explanation:

- **analogRead**: Reads the voltage from LM35 (which outputs 10mV per °C).
- **Serial.println()**: Sends the reading to the serial monitor so Edge Impulse can read it.
- **delay(100)**: Controls the sampling frequency to 10Hz.

---

## 4. Create Project in Edge Impulse

1. Go to [Edge Impulse Studio](https://studio.edgeimpulse.com).
2. Log in or sign up.
![1](https://github.com/user-attachments/assets/1d755236-42a3-4617-bb03-68247bcadd2c)

3. Click on **"Create new project"**.
4. Name your project (e.g., `LM35 Temp Detection`).
5. Click **"Create project"** to continue.
![2](https://github.com/user-attachments/assets/ea57ad5e-2836-4d14-8700-c4fa1820c297)

---

## 5. Install Edge Impulse CLI
### Verify Node.js Installation

Open **Command Prompt as Administrator** and run:

```bash
node -v
npm -v
```

Make sure it shows:
- Node.js version like `v20.x.x`
- npm version like `10.x.x`

---

### Install Edge Impulse CLI

To install the CLI tools globally, run:

```bash
npm install -g edge-impulse-cli
```
This allows you to use commands like `edge-impulse-data-forwarder`.

![3](https://github.com/user-attachments/assets/c96669d6-4c6d-4ee3-a97f-b1e718253375)

Here select Devices and you will see different sources to collect data (like phone, Computer or development board) and select connect to development boards.

---

### Connect ESP32 using Data Forwarder

Connect your ESP32 to the PC via USB and open the Data Forwarder CLI:

```bash
edge-impulse-data-forwarder
```

You will be asked for:

- Your **Edge Impulse username/email**
- Your **password**
- Select your ESP32's **COM port** (e.g., COM6)

---

### Configure the Data Forwarder

If you see an error like:

```
[SER] Failed to get information off device...
```

This means it couldn't auto-detect your sensor’s data frequency.

#### ✅ Fix:

Use the `--frequency` flag. If your `Arduino delay()` is 100 ms:

```bash
edge-impulse-data-forwarder --frequency 10
```

(`1000 / 100ms = 10 Hz`)

---

## 6: Final Configuration

After a successful connection, you’ll be prompted:

```
? 1 sensor axes detected...
  What do you want to call them? → Temp

? What name do you want to give this device? → ESP32-LM35
```

Your device is now linked to Edge Impulse! A link will be displayed to open the Edge Impulse Studio.
![4](https://github.com/user-attachments/assets/66aefb6a-57dd-48f5-b8b9-dbf2aa6bffe4)

---

## 7. Collect Data

1. Go to the **Data Acquisition** tab in Edge Impulse.
2. Start sampling from the connected ESP32.
3. Label your data samples.
4. Collect at least 3 minutes of data for each label.
5. Go to Dashboard Section and perform test/train split.
   
![5](https://github.com/user-attachments/assets/984eeb35-9f0e-4046-b8e6-9c4f4e4e8ba5)

---

## 8. Train the Model

1. Navigate to the **Impulse Design** tab.![6](https://github.com/user-attachments/assets/61fa695b-9df5-4db4-ae6f-cd9804e8794a)

2. Set up input as a single-axis sensor (1 value).
3. Add a **Flatten** block.![8![9](https://github.com/user-attachments/assets/a8f5c041-2fb0-4607-8642-7d5cfde0c066)
](https://github.com/user-attachments/assets/a9a1c68d-da10-415e-bd2e-defdd37607f0)

Here save the parameter and generate features.

4. Add a **Classification** block.![10](https://github.com/user-attachments/assets/e113a2e1-ea16-4c5b-b920-e9fc0be2591f)         
   Save and train the model.
   
6. Go to **Re-Training**, click **Start Training**.
![11](https://github.com/user-attachments/assets/9e685542-1278-4e72-8144-7bbcfd1d80a1)

---

## 9. Live Classification

1. Go to **Live Classification**.
2. Click on start sampling to stream data again from your ESP32.
3. See how well the model predicts live data.
![12](https://github.com/user-attachments/assets/23e0cd45-ad09-4247-bc72-df7cd7b1e80c)

---

## 10.Model Testing
1. Go to **Model Testing**.
2. Click on classify all.
![13](https://github.com/user-attachments/assets/1c5540da-9b6d-4f7c-8f8d-d54673250775)

## 11. Deployment

You can deploy the model back to ESP32 as:
- Go to **Deployment**
- An Arduino library
- To compare model accuracy, run model testing for all available optimizations.
- Click on build (A zip file will be downladed).
![14](https://github.com/user-attachments/assets/77734780-da6c-42e8-b761-7a7af887f119)

###  Add Arduino Library and Test
In Arduino IDE:

Go to Sketch > Include Library > Add .ZIP Library...

Select the downloaded .zip file

Go to:

File > Examples > temperature1_inferencing > esp32 >esp32 fusion
Replace the code with below given code
```cpp

/* Includes ---------------------------------------------------------------- */
#include <Arduino.h>
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>
#include <Temperature1_inferencing.h>  // Your Edge Impulse model

#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
#define OLED_RESET -1
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, OLED_RESET);

#define LM35_PIN 34       // LM35 connected to GPIO 34
#define LED_PIN 2         // Built-in LED on GPIO 2
#define SAMPLE_INTERVAL_MS 100
#define NUM_SAMPLES EI_CLASSIFIER_DSP_INPUT_FRAME_SIZE

float features[NUM_SAMPLES];

void setup() {
  Serial.begin(115200);
  analogReadResolution(12);  // For ESP32 12-bit ADC
  pinMode(LED_PIN, OUTPUT);

  // OLED setup
  if (!display.begin(SSD1306_SWITCHCAPVCC, 0x3C)) {
    Serial.println("❌ OLED init failed");
    while (1);
  }
  display.clearDisplay();
  display.setTextColor(SSD1306_WHITE);
  display.setTextSize(1);
  display.setCursor(0,0);
  display.println("Edge Impulse LM35");
  display.display();

  delay(1000);
}

void loop() {
  Serial.println("\n📡 Sampling LM35 for 4.3 seconds...");
  display.clearDisplay();
  display.setCursor(0, 0);
  display.println("Sampling...");

  // Sample temperature data
  for (size_t i = 0; i < NUM_SAMPLES; i++) {
    int adc = analogRead(LM35_PIN);
    float voltage = (adc * 3.3f) / 4095.0f;
    float temp_c = voltage * 100.0f;
    features[i] = temp_c;

    delay(SAMPLE_INTERVAL_MS);
  }

  // Create signal
  signal_t signal;
  int err = numpy::signal_from_buffer(features, NUM_SAMPLES, &signal);
  if (err != 0) {
    Serial.println("❌ Signal error.");
    return;
  }

  // Run inference
  ei_impulse_result_t result = {0};
  err = run_classifier(&signal, &result, false);
  if (err != EI_IMPULSE_OK) {
    Serial.print("❌ Inference error: ");
    Serial.println(err);
    return;
  }

  // Print and display results
  Serial.println("✅ Inference Result:");
  display.clearDisplay();
  display.setCursor(0, 0);
  display.println("Result:");

  String top_label = "";
  float top_score = 0;

  for (size_t i = 0; i < EI_CLASSIFIER_LABEL_COUNT; i++) {
    Serial.print("  ");
    Serial.print(result.classification[i].label);
    Serial.print(": ");
    Serial.println(result.classification[i].value, 4);

    display.print(result.classification[i].label);
    display.print(": ");
    display.println(result.classification[i].value, 3);

    if (result.classification[i].value > top_score) {
      top_score = result.classification[i].value;
      top_label = result.classification[i].label;
    }
  }

#if EI_CLASSIFIER_HAS_ANOMALY == 1
  Serial.print("🔍 Anomaly score: ");
  Serial.println(result.anomaly, 3);
  display.print("Anomaly: ");
  display.println(result.anomaly, 3);
#endif

  // LED Logic: Blink if "Abnormal"
  if (top_label == "Abnormal") {
    digitalWrite(LED_PIN, HIGH);
    display.println("Status: Abnormal");
  } else {
    digitalWrite(LED_PIN, LOW);
    display.println("Status: Normal");
  }

  display.display();
  delay(1000);  // Wait before next inference
}
```
- Click on Verify
- Upload
- Open Serial monitor and verify the results and also observe the result in OLED display

Upload the example code and monitor results on Serial Monitor.
---
## Results 

![WhatsApp Image 2025-08-01 at 11 52 08 AM](https://github.com/user-attachments/assets/308e5942-10b6-46e0-a514-82db06fc55e5)

![15](https://github.com/user-attachments/assets/623d6473-1319-4c25-a927-11e029ab3013)

## Notes

- LM35 sensor outputs 10mV per degree Celsius.
- ESP32 ADC reads values between 0-4095, which can be converted to °C using:  
  ```temperature = (analogRead * 3.3 / 4095) * 100;```

---

## Resources

- [RDL Technologies Pvt.Ltd](https://researchdesignlab.com/development-baord/esp32/esp32-development-board-trainer-kit.html)
- [Edge Impulse Docs](https://docs.edgeimpulse.com/)
- [LM35 Datasheet](https://www.ti.com/lit/ds/symlink/lm35.pdf)
- [ESP32 Arduino Core](https://github.com/espressif/arduino-esp32)
- [Python Download](https://www.python.org/downloads/)
- [Node.js](https://nodejs.org/en/download)
---

## License

MIT License.
