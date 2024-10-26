# Three-Smart-Switch-Board
The 3-Way Smart Switch Board offers flexible control of lights and fans with Manual, Automatic, and Wi-fi modes. It uses MOSFET-based switching, PWM for fan speed control, and includes integrated temperature and humidity sensors for enhanced automation

#include <DHT.h>
#include <DHT_U.h>
// Digital pin connected to the DHT sensor
  // DHT 11

#define MotPWM 9
#define LEDpinOn 5
#define SocketOn 4
#define ModeSelector A3
#define LDR A2
#define DHTPIN A1
#define FanRegulator A0


#define DHTTYPE DHT11 
DHT dht(DHTPIN, DHTTYPE);
int FanSpeed= 9;
int LEDGlow= 6;
int input = 0;
int threshold =330;
int cntrld=0;
void mode1(){
   int Regulator=analogRead(FanRegulator);
    cntrld=map(Regulator,0,1023,0,255);
    analogWrite(MotPWM,cntrld);
    digitalWrite(LEDpinOn,HIGH);
    digitalWrite(SocketOn,HIGH);
}

void mode2(){
     delay(2000);  // Wait for 2 seconds between measurements

  // Read temperature as Celsius
  float tempC = dht.readTemperature();
  // Read temperature as Fahrenheit
  float tempF = dht.readTemperature(true); // Uncomment to read Fahrenheit instead of Celsius
  // Read humidity
  float humidity = dht.readHumidity();

  // Check if any reads failed and exit early (to try again)
  if (isnan(tempC) || isnan(tempF) || isnan(humidity)) {
    Serial.println("Failed to read from DHT sensor!");
    return;
  }

  // Compute heat index
  float heatIndex = dht.computeHeatIndex(tempC, humidity, false);

  // Print temperature and humidity values to the serial monitor
//  Serial.print("Temperature: ");
//  Serial.print(tempC);
//  Serial.print(" °C\t");
//  Serial.print("Humidity: ");
//  Serial.print(humidity);
//  Serial.print("%\t");
if(tempC>20){
  analogWrite(MotPWM,10*tempC);
}
else{
  analogWrite(MotPWM,1*tempC);
}
////////////////////////////////////////
int luminosity=analogRead(LDR);

if(luminosity<threshold){
   digitalWrite(LEDpinOn,HIGH);
  
  
}
else{
  digitalWrite(LEDpinOn,LOW);
 
}

 
}

void mode3(){
   input=Serial.read();
    Serial.println(input);
    analogWrite(FanSpeed,input);
    analogWrite(LEDGlow,input);
    if(input == 60 )
      digitalWrite(12,HIGH);
    else if(input == 70 )
      digitalWrite(12,LOW); 
    else if(input == 80 )
      digitalWrite(13,HIGH);  
    else if(input == 90)
      digitalWrite(13,LOW);   
}


void setup(){
dht.begin();
pinMode(LEDpinOn, OUTPUT);
// pinMode(LEDpinOff, OUTPUT);
pinMode(SocketOn, OUTPUT);
// pinMode(SocketOff, OUTPUT);
pinMode(FanSpeed, OUTPUT);
pinMode(LEDGlow, OUTPUT);
digitalWrite(LEDGlow,0);
digitalWrite(FanSpeed,0);

Serial.begin(9600);
Serial.println("GROUP 5 IE LAB");
}
void loop(){
  int Mode=analogRead(ModeSelector);
 if(Mode<341){
        Serial.println("mode1");
    mode1();
  }
  if((Mode>341) && (Mode<682)){
        Serial.println("mode2");
    mode2();
  }
  if(Mode>682){
        Serial.println("mode3");
    mode3();
  }
delay(1000);


}
