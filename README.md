#include "DHT.h"

#define DHTPIN 4
#define DHTTYPE DHT22
#define SOIL1 34
#define SOIL2 35
#define RELAY 5

DHT dht(DHTPIN, DHTTYPE);

bool pumpState = false;

void setup() {
  Serial.begin(115200);
  pinMode(RELAY, OUTPUT);
  dht.begin();
}

void loop() {
  int soil1 = analogRead(SOIL1);
  int soil2 = analogRead(SOIL2);
  
  float moisture1 = map(soil1, 0, 4095, 100, 0); // 0=dry, 100=wet
  float moisture2 = map(soil2, 0, 4095, 100, 0);
  
  float temp = dht.readTemperature();
  float hum = dht.readHumidity();

  // Control logic
  if ((moisture1 < 30) || (moisture2 < 30)) {
    pumpState = true;
  }
  if ((moisture1 > 40) && (moisture2 > 40)) {
    pumpState = false;
  }

  digitalWrite(RELAY, pumpState ? HIGH : LOW);

  Serial.print("Moisture1: "); Serial.print(moisture1);
  Serial.print("% | Moisture2: "); Serial.print(moisture2);
  Serial.print("% | Pump: "); Serial.print(pumpState ? "ON" : "OFF");
  Serial.print(" | Temp: "); Serial.print(temp);
  Serial.print("°C | Humidity: "); Serial.println(hum);
  

  delay(2000);
}
