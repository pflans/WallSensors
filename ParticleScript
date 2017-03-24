#define UNITNAME 000

// ---- External Libraries ----
#include <Adafruit_DHT.h>
#include <HttpClient.h>
#include <SdFat.h>
#include <SparkFunMAX17043.h>


// ---- HTTP ----
HttpClient http;
#define SENSOR1_TEMP "xxxxx"
#define SENSOR1_HUM "xxxxx"

#define SENSOR2_TEMP "xxxxx"
#define SENSOR2_HUM "xxxxx"

#define SENSOR3_TEMP "xxxxx"
#define SENSOR3_HUM "xxxxx"

#define SENSOR4_TEMP "xxxxx"
#define SENSOR4_HUM "xxxxx"

#define BATTERY_PCT "xxxxx"
#define BATTERY_VOLT "xxxxx"

#define TOKEN "xxxxx"

http_header_t headers[] = {
      { "Content-Type", "application/json" },
      { "X-Auth-Token" , TOKEN },
    { NULL, NULL } // NOTE: Always terminate headers with NULL
};

http_request_t request;
http_response_t response;
String postPackage;

// ---- SENSORS ----
// Connect pin 1 (on the left) of the sensor to +5V
// Connect pin 2 of the sensor to whatever your DHTPIN is
// Connect pin 4 (on the right) of the sensor to GROUND
// Connect a 10K resistor from pin 2 (data) to pin 1 (power) of the sensor

#define DHTPIN1 5     // what pin we're connected to
#define DHTPIN2 4     
#define DHTPIN3 6     
#define DHTPIN4 3     
#define DHTTYPE DHT22 // DHT 22 (AM2302)

DHT dht1(DHTPIN1, DHTTYPE);
DHT dht2(DHTPIN2, DHTTYPE);
DHT dht3(DHTPIN3, DHTTYPE);
DHT dht4(DHTPIN4, DHTTYPE);

float h1 =  0;
double f1 = 0;

float h2 =  0;
double f2 = 0;

float h3 =  0;
double f3 = 0;

float h4 =  0;
double f4 = 0;

float volt = 0;
float pct = 0;


// ---- TIME ----
const long oneSecond = 1000;  // a second is a thousand milliseconds
const long oneMinute = oneSecond * 60;
const long oneHour   = oneMinute * 60;
const long oneDay    = oneHour * 24;


// ---- SDCARD ----
// Primary SPI with DMA
// SCK => A3, MISO => A4, MOSI => A5, SS => A2 (default)
SdFat sd;
const uint8_t chipSelect = SS;
File dataFile;
File myFile;

// ---- DEBUG ----
bool debug = false;

// ---- FIRMWARE ----
SYSTEM_MODE(SEMI_AUTOMATIC);

void setup() {
    if (debug){
        Serial.println("SETUP");
    }
    
    request.hostname = "things.ubidots.com";
    request.port = 80;
    
    Serial.begin(9600);
    dht1.begin();
    dht2.begin();
    dht3.begin();
    dht4.begin();

    lipo.begin(); // Initialize the MAX17043 LiPo fuel gauge
    lipo.quickStart();
}

void loop() {   
    
    //
    // PART 1: DATA LOGGING
    //
    
    delay(oneSecond * 5);

    if (debug){
        Serial.println("SETUP");
    } 
    
	h1 = dht1.getHumidity();
	f1 = dht1.getTempFarenheit();
	h2 = dht2.getHumidity();
	f2 = dht2.getTempFarenheit();
	h3 = dht3.getHumidity();
	f3 = dht3.getTempFarenheit();
	h4 = dht4.getHumidity();
	f4 = dht4.getTempFarenheit();


	pct = lipo.getSOC();
	volt = lipo.getVoltage();
    
    if (debug){
        if (isnan(h1) || isnan(f1)) {
		    Serial.println("Failed to read from DHT SENSOR 1!");
		} else {
		    Serial.println("SENSOR_1");
            Serial.println(f1);
            Serial.println(h1);
		}
		if (isnan(h2) || isnan(f2)) {
		    Serial.println("Failed to read from DHT SENSOR 2!");
		} else {
            Serial.println("SENSOR_2");
            Serial.println(f2);
            Serial.println(h2);
		}
		if (isnan(h3) || isnan(f3)) {
		    Serial.println("Failed to read from DHT SENSOR 3!");
		} else {
            Serial.println("SENSOR_3");
            Serial.println(f3);
            Serial.println(h3);
		}
		if (isnan(h4) || isnan(f4)) {
		    Serial.println("Failed to read from DHT SENSOR 4!");
		} else {
            Serial.println("SENSOR_4");
            Serial.println(f4);
            Serial.println(h4);
		}
		if (isnan(volt) || isnan(pct)) {
		    Serial.println("Failed to read from BATTERY FUEL GAUGE!");
		} else {
            Serial.println("BATTERY VOLT");
            Serial.println(volt);
            Serial.println("BATTERY PCT");
            Serial.println(pct);
		}
    }
    
    String fileTime = Time.format("%d-%m-%y");
    String readTime = String(Time.now()) + "000";
    
    sd.begin(chipSelect, SPI_HALF_SPEED);
    dataFile.open(fileTime+".txt", O_RDWR | O_CREAT | O_AT_END);
    
    // UNIT_NUMBER, READ_TIME, SENSOR_1_TEMP, SENSOR_1_HUM, SENSOR_2_TEMP, SENSOR_2_HUM, SENSOR_3_TEMP, SENSOR_3_HUM, BATTERY_PCT, BATTERY_VOLT
    // {"variable": "568405d376254261ef114f35", "value":41.2}, {"variable": "56843ac776254258956b9c05", "value":88.3} 
    postPackage =
        "[{\"variable\": \"" + String(SENSOR1_TEMP) + "\", \"timestamp\": " + readTime + ", \"value\": " + String(f1) + "}" 
        + "," +
        "{\"variable\": \"" + SENSOR2_TEMP + "\", \"timestamp\": " + readTime + ", \"value\": " + String(f2) + "}" 
        + "," + 
        "{\"variable\": \"" + SENSOR3_TEMP + "\", \"timestamp\": " + readTime + ", \"value\": " + String(f3) + "}" 
        + "," + 
        "{\"variable\": \"" + SENSOR4_TEMP + "\", \"timestamp\": " + readTime + ", \"value\": " + String(f4) + "}"
        + "," + 
        "{\"variable\": \"" + SENSOR1_HUM + "\", \"timestamp\": " + readTime + ", \"value\": " + String(h1) + "}" 
        + "," +
        "{\"variable\": \"" + SENSOR2_HUM + "\", \"timestamp\": " + readTime + ", \"value\": " + String(h2) + "}" 
        + "," + 
        "{\"variable\": \"" + SENSOR3_HUM + "\", \"timestamp\": " + readTime + ", \"value\": " + String(h3) + "}" 
        + "," + 
        "{\"variable\": \"" + SENSOR4_HUM + "\", \"timestamp\": " + readTime + ", \"value\": " + String(h4) + "}" 
        + "," + 
        "{\"variable\": \"" + BATTERY_PCT + "\", \"timestamp\": " + readTime + ", \"value\": " + String(pct) + "}" 
        + "," + 
        "{\"variable\": \"" + BATTERY_VOLT + "\", \"timestamp\": " + readTime + ", \"value\": " + String(volt) + "}]";
        
    dataFile.println(postPackage);
    dataFile.close();
    
    if (debug){
        Serial.println("POST_PACKAGE");
        Serial.println(postPackage);
    }
    
    // 
    // PART 2: SENDING DATA
    //
    
    if (debug){
        WiFi.on();
        if (Particle.connected() == false) {
            Particle.connect();
        }
        
        delay(oneSecond * 15);
            
        Serial.println("WIFISTATUS:");
        Serial.println(WiFi.ready());
        
        if (debug){
            Serial.println("FILE");
            Serial.println(fileTime+".txt");
        }
        
        if (!dataFile.open(fileTime+".txt", O_READ)) {
            if (debug){
                sd.errorHalt("opening "+fileTime+".txt for read failed");
            }
        }
    
        while (dataFile.available()) {
          String line = dataFile.readStringUntil('\n');
          Serial.println("LINE");
          Serial.println(line);
          
          request.path = "/api/v1.6/collections/values";
          request.body = line;
          http.post(request, response, headers);
         
          Serial.println("RESPONSE");
          Serial.println( response.status );
          delay(oneSecond);
        }
        
        dataFile.close();
    } else {
        if (Time.hour() == 23){
            WiFi.on();
            if (Particle.connected() == false) {
                Particle.connect();
            }
           
            delay(oneSecond * 15);
            
            if (!dataFile.open(fileTime+".txt", O_READ)) {
                if (debug){
                    sd.errorHalt("opening "+fileTime+".txt for read failed");
                }
            }
        
            while (dataFile.available()) {
              String line = dataFile.readStringUntil('\n');
              
              request.path = "/api/v1.6/collections/values";
              request.body = line;
              http.post(request, response, headers);
              delay(oneSecond);
            }
            
            dataFile.close();
            WiFi.off();
        }
    }

    if (!debug){
        delay(oneMinute);
        Particle.sleep(SLEEP_MODE_DEEP, 3600);
    } else {
        Serial.println("WIFISTATUS:");
        Serial.println(WiFi.ready());
        Serial.println("END_LOOP");  
        delay(oneSecond * 30);
    }
    
    
// Compute heat index
// Must send in temp in Fahrenheit!
/*
	hi = dht.getHeatIndex();
	dp = dht.getDewPoint();
	k = dht.getTempKelvin();
*/
    
}
