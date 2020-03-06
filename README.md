# maestro#include <SoftwareSerial.h>
SoftwareSerial mensaje(2,3);
#include <Servo.h>
Servo myservo;
#include<LCD.h>
#include <LiquidCrystal_I2C.h> 
LiquidCrystal_I2C lcd (0x27, 2, 1, 0, 4, 5, 6, 7);
double ap = 0, ai = 0, ad = 0, output, input = 0, error = 0, errora = 0, setpoint = 10, kp = 10, ki = 0.01, kd = 0, sensor = 0, tm = 0.01, salida = 0;
double op=0;

void setup() {
  Serial.begin(9600); 
  delay(200);
  mensaje.begin(9600);
  myservo.attach(12);

}
void pid() {
   if(mensaje.available()){
   double msj=mensaje.read();
   Serial.println(msj);
  error = setpoint - msj;
  ap = kp * error;
  ai = ki * ((tm * errora) + (tm * (error - errora)) / 2);
  ad = kd * ((error - errora) / tm);
  output += ap + ai + ad;
  if (output > 100) {
    output = 100;
  }
  if (output < 0) {
    output = 0;
  }
  errora = error;
   }
}

void loop() {
   pid();
   salida = map(output, 0, 100, 90, 170);
   myservo.write(salida);
  delay(100);
   
}
