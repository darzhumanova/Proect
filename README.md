// nokia 5110 үшін кітапханаларды қосу
 #include 
 #include 
 // DHT кітапханасын қосу
 #include "DHT.h" 
 // DHT сенсорының түрі
 #define DHTTYPE DHT11

// DHT11 модулінің деректерді енгізу қосылымына хабарласыңыз 
 int pinDHT11=9; 
 // топырақ ылғалдылығы модулінің аналогтық шығысын қосуға арналған байланыс
 int pinSoilMoisture=A0; 
 // TMP36 температура сенсорының аналогтық шығыс қосылымының түйреуіші
 int pinTMP36=A1; 
 // Фоторезистордың аналогтық шығысын қосуға арналған түйреуіш
 int pinPhotoresistor=A2;

// индикаторлық жарықдиодты түйреуіштер
 #define LED_TEMP 5
 #define LED_MOISTURE 6
 #define LED_LIGHT 7

// шарттардың мәндері
 #define TEMP_DETECT 30
 #define MOISTURE_DETECT 500
 #define LIGHT_DETECT 250

// DHT нысанының данасын жасау
 DHT dht(pinDHT11, DHTTYPE);

// Nokia 5110
 // pin 13 - Serial clock out (SCLK)
 // pin 12 - Serial data out (DIN)
 // pin 11 - Data/Command select (D/C)
 // pin 10 - LCD chip select (CS)
 // pin 8 - LCD reset (RST)
 Adafruit_PCD8544 display = Adafruit_PCD8544(13, 12, 11, 10, 8);

void setup() { 
 // сериялық портты іске қосу
 Serial.begin(9600);
 //
 pinMode(LED_TEMP,OUTPUT);digitalWrite(LED_TEMP,LOW);
 pinMode(LED_MOISTURE,OUTPUT);digitalWrite(LED_MOISTURE,LOW);
 PinMode(LED_LIGHT,OUTPUT);digitalWrite(LED_LIGHT, LOW); 
// 
dht.begin(); 
 // дисплейді инициализациялау
 display.begin(); 
 // экран фонының контрастын орнатыңыз
 display.setContrast(60);
 display.clearDisplay(); // экранды тазалау
 display.setTextSize(1); // қаріп өлшемі 
 display.setTextColor(BLACK); // түс 
 // экран сақтағышы
 display.setCursor(15,15);
 display.print("Home Flower");
 display.display();
 delay(2000);
 }

void loop()
 {
 display.clearDisplay();
 display.setCursor(5,0);
 display.print ("Үй гүлі"); 
 // DHT11 арқылы деректерді алу
 қалқымалы h = dht.readHumidity(); 
display.setCursor(5,10); 
 егер (isnan(h)) 
{ 
 Serial.println("DHT-тен оқу мүмкін болмады"); 
 display.print("AirH= error"); 
} 
басқа 
{ 
 Serial.print("HumidityDHT11= "); Serial.print(h);Serial.println(" %"); 
display.print("AirH=");display.print(h);display.print("%"); 
} 
 // топырақ ылғалдылығы модулінің аналогтық шығысынан мәнді алу
 display.setCursor(5,20);
 int val0=analogRead(pinSoilMoisture);
 Serial.print("SoilMoisture= "); Serial.println(val0);
 display.print("soilM=");display.print(val0);
 // TMP36 температура сенсорының аналогтық шығысынан мәнді алу
 display.setCursor(5,30);
 int val1=analogRead(pinTMP36);
 // мВ-ға аудару
 int mV = val1*1000/1024; 
 // Цельсий градусына аудару
 int t=(mV-500)/10+75;// t=23; 
 Serial.print("TempTMP36= "); Serial.print(t);Serial.println("C"); 
 display.print("airT=");display.print(t);display.print("C"); 
 // фоторезистордың аналогтық түйреуішінен мән алу
 display.setCursor(5,40);
 int val2=analogRead(pinPhotoresistor);
 Serial.print("Light= "); Serial.println(val2);
 display.print("Light=");display.print(val2);
 // жаңарту 
display.display(); 
 //// шарттарды тексеру 
 // топырақтың ылғалдылығы 
 егер(val0 > MOISTURE_DETECT) 
 digitalWrite(LED_MOISTURE, HIGH);
 басқа 
digitalWrite(LED_MOISTURE, LOW); 
 // ауа температурасы 
 егер(t > TEMP_DETECT) 
 digitalWrite(LED_TEMP, ЖОҒАРЫ);
 else
 digitalWrite(LED_TEMP,LOW);
 // освещенность
 if(val2 < LIGHT_DETECT)
 digitalWrite(LED_LIGHT,HIGH);
 басқа 
digitalWrite(LED_LIGHT, LOW); 
 // 5 секунд үзіліс
 Serial.println();
 delay(5000);
 }
