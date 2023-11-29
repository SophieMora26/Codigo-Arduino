# Codigo-Arduino#include <SD.h>
#include <stdio.h>
#include <DS1302.h>
#include <DHT.h>

#define DHTTYPE DHT11

const int DHTPin = 3;

const int RST   = 5;  
const int DAT   = 6;  
const int CLK = 7;  

DHT dht(DHTPin, DHTTYPE);
DS1302 rtc(RST, DAT, CLK);

File myFile;
void setup()
{
  pinMode(8,INPUT);
  pinMode(2,OUTPUT);
  pinMode(4,OUTPUT);
  dht.begin();
  delay(3000);
  Serial.begin(9600);
}

void loop()
{
  float humedad = dht.readHumidity();
        float temperatura = dht.readTemperature();
   Serial.print("Temperature: ");
        Serial.print(temperatura);
        Serial.println(" °C");

        Serial.print("Humidity: ");
        Serial.print(humedad);
        Serial.println(" %");
  if (digitalRead(8)==1){
     digitalWrite(2,HIGH);
     SD.begin(10);
     myFile = SD.open("datalog.txt", FILE_WRITE);
     if (myFile) {
        float humedad = dht.readHumidity();
        float temperatura = dht.readTemperature();
        int luz = analogRead(0);
        Time t = rtc.time();
       
        myFile.print(t.yr);
        myFile.print(";");
        myFile.print(t.mon);
        myFile.print(";");
        myFile.print(t.date);
        myFile.print(";");
        myFile.print(t.hr);
        myFile.print(";");
        myFile.print(t.min);
        myFile.print(";");
        myFile.print(t.sec);
        myFile.print(";temperatura;");
        myFile.print(temperatura);
        myFile.print(";humedad;");
        myFile.print(humedad);
        myFile.print(";luz;");
        myFile.println(luz);
        myFile.close();                         
        }
        if (SD.exists("datalog.txt")){
          digitalWrite(4,HIGH);
          }
          else{
            digitalWrite(4,LOW);
            }
        delay(1000);
     }
    else{
      digitalWrite(2,LOW);
      digitalWrite(4,LOW);
      }
}
