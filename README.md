# people-counting-system
#include <LCD_I2C.h>
LCD_I2C lcd(0x27, 16, 2);
#include <DHT.h>
#define DHT11_PIN 7
#define DHT_TYPE DHT11
DHT dht (DHT11_PIN, DHT_TYPE);
const int waterPumpRelay = 3; // Water pump relay pin
#define gas A2
#define water A0
#define vibration_motor 8
String a;
float pHValue = 0;
float pHVolts = 0;
void setup() {
 Serial.begin(9600);
 lcd.begin();  // Assuming a 16x2 LCD
  lcd.backlight();
  dht.begin();
  pinMode(DHT11_PIN,INPUT);
  pinMode(gas,INPUT);
  pinMode(water,INPUT);
  pinMode(vibration_motor,OUTPUT);

}
void loop() {
 
  if (Serial.available() > 0) {
    a = Serial.readString(); 
    int phIndex = a.indexOf("PH:");
    if (phIndex != -1) {
      int commaIndex = a.indexOf(',', phIndex);
      if (commaIndex != -1) {
        String pHValueStr = a.substring(phIndex + 3, commaIndex); // extract pH value substring
        float pHValue = pHValueStr.toFloat(); // convert String to float
        Serial.println("PH: " + pHValueStr);
        if (pHValue < 8){
         Serial.println("good water");
        }
        else if (pHValue >8 && pHValue <15){
        Serial.println("bad water");
       }
       else{

       }
   
      }
    }
 }

   
 //read temperature sensor 
  float t = dht.readTemperature();
  int h = dht.readHumidity();
  Serial.println();
  Serial.println("temperature");
  Serial.println(t);
  Serial.println("humidity");
  Serial.println(h);
  delay(1000);

  
   lcd.setCursor(0,0);
  lcd.print("temperature");
  lcd.setCursor(0,1);
  lcd.print(t);
  delay(1000);
  lcd.clear();
  delay(1000);


lcd.setCursor(0,0);
  lcd.print("humidity");
  lcd.setCursor(0,1);
  lcd.print(h);
 delay(1000);
  lcd.clear();
  delay(1000);
  

  //read water sensor
  int b=analogRead(water);
  Serial.println("water level");
  Serial.println(b);
  delay(1000);

  
   lcd.setCursor(0,0);
  lcd.print("water level");
  lcd.setCursor(0,1);
  lcd.print(b);
  delay(1000);
  lcd.clear();
  delay(1000);

  if(b<=500){
     Serial.println("Object Detected - Water Pump ON");
    digitalWrite(waterPumpRelay, HIGH); // Turn on water pump
  }
  else{
   digitalWrite(waterPumpRelay, LOW); 
  }

  //read gas sensor
  int c=analogRead(gas);
  Serial.println("gas level");
  Serial.println(c);
  delay(1000);

   lcd.setCursor(0,0);
  lcd.print("gas level");
  lcd.setCursor(0,1);
  lcd.print(c);
  delay(1000);
  lcd.clear();
  delay(1000);


  if(c>=700){
    digitalWrite(vibration_motor,HIGH);
  }
  else{
    digitalWrite(vibration_motor,LOW);
  }
  
  Serial.print("");


}
