# Enter_in_2_IOT-DHT11
# Developing web interface for temperature and Humidity using ESP01 and DHT11 sensor

#include <ESP8266WiFi.h>
#include <WiFiClient.h>
#include <ESP8266WebServer.h>
#include <DHT.h>
#define DHTTYPE DHT11
#define DHTPIN 2

// Enter your router's SSID and PASSWORD.

const char* ssid = ".......";
const char* pwd = "........";

ESP8266WebServer server(80);

DHT dht(DHTPIN, DHTTYPE, 11);

float humidity, temp_f, temp_c;           // to store the values recorded from the sensor.
String webstring = "";                    // string to display.
unsigned long previous = 0;         // stores the time when last sensor reading was recorded (in ms).
const long interval = 2000;               // Time interval for subsequent sensor readings (in ms).

void handle_root() {
  server.send(200, "text/", "Temperature and Humidity");
  delay(100);
}

void gettemperature() {
  unsigned long current = millis();
  if(current - previous >= interval)  {
      previous = current;
      humidity = dht.readHumidity();
      temp_f = dht.readTemperature(true);
      temp_c = (((temp_f - 32)* 5)/ 9);
      if( isnan(humidity) || isnan(temp_f)) {
            Serial.printIn("Failed to read from sensor");
            return;
      }
  }
}

void setup(void) {
  Serial.begin(115200);
  dht.begin();
  WiFi.begin(ssid, pwd);
  Serial.print("\n Connecting....");
  while(WiFI.status() != WL_CONNECTED)
