
# IOT-Smart-Parking
ระบบตรวจสอบช่องจอดรถผ่าน LINE Notify

สิ่งจำเป็น
1. NodeMCU ESP8266
2. HC-SR04 Ultrasonic Distance
3. 1602 LCD (Yellow Screen) 16x2 LCD + I2C Adapter
4. LINE Notify : https://notify-bot.line.me/th/

**[LINE ประกาศเตรียมยุติการให้บริการ LINE Notify ตั้งแต่วันที่ 31 มี.ค 2568 เป็นต้นไป](https://linedevth.line.me/th/knowledge-api/discontinued-line-notify#:~:text=LINE%20Notify%20%E0%B8%9A%E0%B8%A3%E0%B8%B4%E0%B8%81%E0%B8%B2%E0%B8%A3%E0%B8%AA%E0%B9%88%E0%B8%87%E0%B8%82%E0%B9%89%E0%B8%AD%E0%B8%84%E0%B8%A7%E0%B8%B2%E0%B8%A1,%E0%B8%AA%E0%B8%B2%E0%B8%A1%E0%B8%B2%E0%B8%A3%E0%B8%96%E0%B9%83%E0%B8%8A%E0%B9%89%E0%B8%87%E0%B8%B2%E0%B8%99%E0%B9%84%E0%B8%94%E0%B9%89%E0%B8%AD%E0%B8%B5%E0%B8%81)**

คลิปตัวอย่างการทำงาน
https://youtu.be/FGMK8T3z94U

# library
- ESP8266WiFi.h : https://github.com/ekstrand/ESP8266wifi
- LiquidCrystal_I2C.h : https://github.com/johnrickman/LiquidCrystal_I2C
- TridentTD_LineNotify.h : https://www.arduinolibraries.info/libraries/trident-td_linenotify

# Code
```

#include <ESP8266WiFi.h>
#include <LiquidCrystal_I2C.h>
#include <TridentTD_LineNotify.h>

#define WIFI_SSID "username"                  //your WiFi SSID(Hotspot Name) for which your NodeMCU connects 
#define WIFI_PASSWORD "password"           //Password of your wifi network 
#define LINE_TOKEN "token"

#define trigPin1 D0   //D0 (We Used GPIO Pin Number)
#define echoPin1 D3   //D3
#define trigPin2 D4    //D4
#define echoPin2 D5    //D5
#define trigPin3 D6    //D6
#define echoPin3 D7   //D7

LiquidCrystal_I2C lcd(0x27, 16, 2);

void setup() 
{
  Serial.begin(115200);            // Select the same baud rate if you want to see the datas on Serial Monitor
  Serial.println(LINE.getVersion());
  pinMode(trigPin1, OUTPUT);
  pinMode(echoPin1, INPUT);
  pinMode(trigPin2, OUTPUT);
  pinMode(echoPin2, INPUT);
  pinMode(trigPin3, OUTPUT);
  pinMode(echoPin3, INPUT);
  
  WiFi.begin(WIFI_SSID,WIFI_PASSWORD);
  Serial.print("connecting");
  while (WiFi.status()!=WL_CONNECTED)
  {
    Serial.print(".");
    delay(500);
  }
  Serial.println();
  Serial.print("connected:");
  Serial.println(WiFi.localIP());


  lcd.begin();                           //begin lcd
  lcd.home();                            
  lcd.setCursor(0, 0);                      // 0th row and 0thh column
  lcd.print("Smart Parking");    

  LINE.setToken(LINE_TOKEN);
}


void loop() 
{
  
  //Ultrasonic Sensor 1

  digitalWrite(trigPin1, LOW);
  delayMicroseconds(10);
  digitalWrite(trigPin1, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin1, LOW);
  long duration1 = pulseIn(echoPin1, HIGH);
  long inch1 = duration1 / 74 / 2;         // In Inch
  
  if(inch1 <=2)
  {
    Serial.println("Park1 = 1");
    lcd.setCursor(0, 1);
    lcd.print("Park1 : 1 car");
    LINE.notify("ช่องจอดที่ 1 มีรถจอดอยู่ครับ");
    delay(1500);   
  }
  else if(inch1 >2)
  {
     Firebase.setInt("Park1",0);
     Serial.println("Park1 = 0");
     lcd.setCursor(0, 1);
     lcd.print("Park1 : 0 car");
     LINE.notify("ช่องจอดที่ 1 ว่างครับ");
     delay(1500);  
  }

  //Ultrasonic Sensor 2

  digitalWrite(trigPin2, LOW);
  delayMicroseconds(10);
  digitalWrite(trigPin2, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin2, LOW);
  long duration2 = pulseIn(echoPin2, HIGH);
  long inch2 = duration2 / 74 / 2;         // In Inch

  if(inch2 <=2)
  {
    Serial.println("Park2 = 1");
    lcd.setCursor(0, 1);
    lcd.print("Park2 : 1 car");
    LINE.notify("ช่องจอดที่ 2 มีรถจอดอยู่ครับ");
    delay(1500);  
  }
  else if(inch2 >2)
  {
     Firebase.setInt("Park2",0);
     Serial.println("Park2 = 0");
     lcd.setCursor(0, 1);
     lcd.print("Park2 : 0 car");
     LINE.notify("ช่องจอดที่ 2 ว่างครับ");
     delay(1500);
  }
  
  //Ultrasonic Sensor 3

  digitalWrite(trigPin3, LOW);
  delayMicroseconds(10);
  digitalWrite(trigPin3, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin3, LOW);
  long duration3 = pulseIn(echoPin3, HIGH);
  long inch3 = duration3 / 74 / 2;         // In Inch
 
  if(inch3 <=2)
  {
    Serial.println("Park3 = 1");
    lcd.setCursor(0, 1);
    lcd.print("Park3 : 1 car");
    LINE.notify("ช่องจอดที่ 3 มีรถจอดอยู่ครับ");
    delay(1500);  
  }
  else if(inch3 >2)
  {
     Serial.println("Park3 = 0");
     lcd.setCursor(0, 1);
     lcd.print("Park3 : 0 car");
     LINE.notify("ช่องจอดที่ 3 ว่างครับ");
     delay(1500);  
  }
  
}
```
