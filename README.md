# ROOM TEMPRATURE-
CODE FOR AURDINO CODE:-
a#include "DHT.h"
#include <SoftwareSerial.h>
#define DHTPIN 2     // what digital pin we're connected to
#define DHTTYPE DHT11   // DHT 11
DHT dht(DHTPIN, DHTTYPE);
SoftwareSerial sw(11, 12);

void setup() 
{
  Serial.begin(9600);
  sw.begin(9600);
  dht.begin();
}

void loop() 
{
 float h = dht.readHumidity();
 float t = dht.readTemperature();
 Serial.print("*");
 Serial.print(t);
 Serial.print(",");
 Serial.print(h);
 Serial.println("#");
 
 sw.print("*");
 sw.print(t);
 sw.print(",");
 sw.print(h);
 sw.println("#");
 delay(10000);
 }
 
 #CODE FOR NODE MCU
 
 /************************ Adafruit IO Config *******************************/

// visit io.adafruit.com if you need to create an account, // or if you need your Adafruit IO key. #define IO_USERNAME "doorth" #define IO_PASSWORD "doorth" #define IO_KEY "e9411606925b403e9da858eb06ede38d"

/******************************* WIFI **************************************/

// the AdafruitIO_WiFi client will work with the following boards: // - HUZZAH ESP8266 Breakout -> https://www.adafruit.com/products/2471 // - Feather HUZZAH ESP8266 -> https://www.adafruit.com/products/2821 // - Feather M0 WiFi -> https://www.adafruit.com/products/3010 // - Feather WICED -> https://www.adafruit.com/products/3056
#define WIFI_SSID "SSID" #define WIFI_PASS "PASS"

// comment out the following two lines if you are using fona or ethernet #include "AdafruitIO_WiFi.h" AdafruitIO_WiFi io(IO_USERNAME, IO_KEY, WIFI_SSID, WIFI_PASS);

/******************************* FONA **************************************/

// the AdafruitIO_FONA client will work with the following boards: // - Feather 32u4 FONA -> https://www.adafruit.com/product/3027

// uncomment the following two lines for 32u4 FONA, // and comment out the AdafruitIO_WiFi client in the WIFI section // #include "AdafruitIO_FONA.h" // AdafruitIO_FONA io(IO_USERNAME, IO_KEY);

/**************************** ETHERNET ************************************/

// the AdafruitIO_Ethernet client will work with the following boards: // - Ethernet FeatherWing -> https://www.adafruit.com/products/3201

// uncomment the following two lines for ethernet, // and comment out the AdafruitIO_WiFi client in the WIFI section // #include "AdafruitIO_Ethernet.h" // AdafruitIO_Ethernet io(IO_USERNAME, IO_KEY);

#DHT IOT ARDUINO NODE MCU MODE

 #include <AdafruitIO_WiFi.h>
#include <ESP8266WiFi.h>
#include <DNSServer.h>
#include <ESP8266WebServer.h>
#include <WiFiManager.h> 
#include <SoftwareSerial.h>

#define IO_USERNAME  "roomth"
#define IO_KEY       "e9411606925b403e9da858eb06ede38d"              
#define WIFI_SSID       ""
#define WIFI_PASS       ""
AdafruitIO_WiFi io(IO_USERNAME, IO_KEY, WIFI_SSID, WIFI_PASS);

SoftwareSerial swSer(D7, D8, false, 256);
String response;
int P1;
int P2;
int current = 0;
int last = -1;
AdafruitIO_Feed *PR1 = io.feed("PR1");
AdafruitIO_Feed *PR2 = io.feed("PR2");

void setup() 
{
  pinMode(LED_BUILTIN, OUTPUT);
  digitalWrite(LED_BUILTIN, LOW);
  Serial.begin(9600);
  swSer.begin(9600);
  while(! Serial);
  WiFiManager wifiManager;
  wifiManager.autoConnect("WIFI MODULE");
  Serial.println("connected...:)");
  Serial.println("");
  Serial.println("WiFi connected");  
  Serial.println("IP address: ");
  Serial.println(WiFi.localIP());
  Serial.print("Connecting to Adafruit IO");
  io.connect();
  while(io.status() < AIO_CONNECTED) {
  Serial.print(".");
  delay(500);
  }
  Serial.println();
  Serial.println(io.statusText());
  delay(2000);
  Serial.end();
  for(int i=0;i<20;i++)
  {
  digitalWrite(LED_BUILTIN, LOW);
  delay(100);  
  digitalWrite(LED_BUILTIN, HIGH); 
  delay(100); 
  }
  Serial.begin(9600);
  while(! Serial);
  }

int ESPwait(String stopstr, int timeout_secs)
{
  bool found = false;
  char c;
  long timer_init;
  long timer;

  response="";
  timer_init = millis();
  while (!found) {
    timer = millis();
    if (((timer - timer_init) / 1000) > timeout_secs) { // Timeout?
      Serial.println("!Timeout!");
      return 0;  // timeout
    }
    if (swSer.available()) {
      c = swSer.read();
      //Serial.print(c);
      response += c;
      if (response.endsWith(stopstr)) {
        found = true;
        delay(10);
        swSer.flush();
        Serial.flush();
        Serial.println();
      }
    } // end Serial1_available()
  } // end while (!found)
  return 1;
}

int ESPwait1(String stopstr, int timeout_secs)
{
  bool found = false;
  char c;
  long timer_init;
  long timer;

  response="";
  timer_init = millis();
  while (!found) {
    timer = millis();
    if (((timer - timer_init) / 1000) > timeout_secs) { // Timeout?
      Serial.println("!Timeout!");
      return 0;  // timeout
    }
    if (Serial.available()) {
      c = Serial.read();
      //Serial.print(c);
      response += c;
      if (response.endsWith(stopstr)) {
        found = true;
        delay(10);
        Serial.flush();
        Serial.println();
      }
    } // end Serial1_available()
  } // end while (!found)
  return 1;
}

void loop() 
{
 char c;
 io.run();
if(swSer.available())
  {  
  c=swSer.read();
  swSer.println(c);
  if(c=='*')
    {
      if(ESPwait("#",3))
      {
      char * strtokIndx;
      response.remove(response.length()-1);
      strtokIndx = strtok(const_cast<char*>(response.c_str()),",");      // get the first part - the string
      P1 = atoi(strtokIndx);
      
      strtokIndx = strtok(NULL, ","); 
      P2 = atoi(strtokIndx);

       Serial.println(P1);
       Serial.println(P2);
       response=""; 
      Serial.println("sending -> ");

    PR1->save(P1);
    PR2->save(P2);
    }
    }
  }


  if(Serial.available())
  {   
  c=Serial.read();
  Serial.println(c);
  if(c=='*')
    {
      if(ESPwait1("#",3))
      {
      char * strtokIndx;
      response.remove(response.length()-1);
      strtokIndx = strtok(const_cast<char*>(response.c_str()),",");      // get the first part - the string
      P1 = atoi(strtokIndx);
      
      strtokIndx = strtok(NULL, ","); 
      P2 = atoi(strtokIndx);
     
       Serial.println(P1);
       Serial.println(P2);
       response=""; 
       Serial.println("sending -> ");
      PR1->save(P1);
      PR2->save(P2);
   }
   }
  }
}
