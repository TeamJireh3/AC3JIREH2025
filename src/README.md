# SOFTWARE DE CONTROL 
====

Aqui se encuentra el programa principal que se utliza en la movilidad del vehiculo autonomo:
#include <Servo.h>
#include <Ultrasonic.h>

#include "SoftwareSerial.h"
#include "DFRobot_HuskyLens.h"

//Códigos de libreria
SoftwareSerial mySerial(A2, A1); // RX, TX
DFRobot_HuskyLens huskylens;

Ultrasonic sensorade(7, 8);// sensor de adelante
Ultrasonic sensorder(3, 2);// sensor de derecha
Ultrasonic sensorizq(4, 5);//sensor de izquierda

//servo //
Servo direc;

// Variables //

// sensores //
int sensor_der;
int sensor_izq;
int sensor_ade;
///////////////////

// Motores //
int motor1A = 10;
int motor2A = 9;
int ena= 6;
//////////////////

// Direccionales //

//Curvas//
float centro = 83;
int derecha = centro + 45;
int izquierda = centro - 45;

//Bloques//
int derechaB = centro + 20;
int izquierdaB = centro - 20;

//Alineamiento//
float der = centro + 6;
float izq = centro - 4;
///////////////////////

//distancia adelante//
bool dist_ade = false; 
//////////////////

//Banco de Curvas//
int vueltas = 0;

void setup() {
  Serial.begin(9600);
  mySerial.begin(9600);
  
  //Motor dc //
  pinMode(motor1A, OUTPUT);
  pinMode(motor2A, OUTPUT);
  pinMode(ena, OUTPUT);

  // digitalWrite(motor1A, HIGH);     
  // digitalWrite(motor2A, LOW);   
  // analogWrite(ena, 200);      

  //Servomotor
  direc.attach(13);
  vueltas = 0;
  
  //  direc.write(centro);

  //HuskyLens
  while (!huskylens.begin(mySerial)) {
    Serial.println("Esperando conexión con HuskyLens...");
    // delay(1000);
  }

  huskylens.writeAlgorithm(ALGORITHM_COLOR_RECOGNITION);
  Serial.println("HuskyLens conectado. Esperando colores...");
}







void loop() {
 // sensores //
  sensor_izq = sensorizq.read();
  sensor_der = sensorder.read();
  sensor_ade = sensorade.read();

  // Imprimir resultado de las distancias
  Serial.print("Distancia ade: ");
  Serial.println(sensor_ade); 

  Serial.print("Distancia izq: ");
  Serial.println(sensor_izq); 

  Serial.print("Distancia der: ");
  Serial.println(sensor_der);

  Serial.print("vueltas= ");
  Serial.println(vueltas);

  // direc.write(centro);
  digitalWrite(motor1A, HIGH);     
  digitalWrite(motor2A, LOW);   
  analogWrite(ena, 170); 


  direc.write(centro);
 
  //HuskyLens → Selección del objeto más grande
  if (huskylens.requestLearned()) {
    int cantidad = huskylens.count();

if (cantidad > 0) {
  HUSKYLENSResult elegido;
    int maxAncho = 0;
     bool encontrado = false;


for (int i = 0; i < cantidad; i++) {
   HUSKYLENSResult temp = huskylens.get(i);
      int ancho = temp.width;


if (ancho > maxAncho) {
maxAncho = ancho;
   elegido = temp;
     encontrado = true;
      Serial.print("tamaño= ");
  Serial.println(maxAncho);
  }
}





 if (encontrado) {
 Serial.print("ID detectado (más grande): ");
 Serial.println(elegido.ID);
//  direc.write(der);
//  delay(50);
  //  direc.write(centro);
  //  delay(50);
//      direc.write(izq);
//    delay(50);



////////////////////////////////////////////////////////////////

//Bloque verde //
if (elegido.ID == 1 && maxAncho >= 35 && sensor_izq > 25 ) {
  direc.write(izquierdaB);
delay(500);
//direc.write(centro);

// digitalWrite(motor1A, LOW);     
//   digitalWrite(motor2A, LOW);   
//   analogWrite(ena, 0); 
   //delay(500);

  // digitalWrite(motor1A, HIGH);     
  // digitalWrite(motor2A, LOW);   
  // analogWrite(ena, 200); 
  direc.write(derechaB);
  delay(850);

direc.write(centro);
     // delay(10000);
     
 // Serial.println("Color 1 detectado → Servo a izquierda");
/////////////////////////////////////////////////////////////////

//Bloque rojo //

  } 
  else if (elegido.ID == 2 && maxAncho >= 40 && sensor_der > 25) {
   direc.write(derechaB);
  delay(500);

  // digitalWrite(motor1A, LOW);     
  // digitalWrite(motor2A, LOW);   
  // analogWrite(ena, 0); 
  // delay(500);

  //   digitalWrite(motor1A, HIGH);     
  // digitalWrite(motor2A, LOW);   
  // analogWrite(ena, 200); 
  direc.write(izquierdaB);
    delay(850);
    direc.write(centro);
     // delay(10000);

         // Serial.println("Color 2 detectado → Servo a derecha");
  }
  /////////////////////////////////////////////////////////////////





     }


     
   } else {
      Serial.println("No hay colores detectados.");
//        direc.write(der);
//  delay(50);
  // direc.write(centro);
  //  delay(50);
  //    direc.write(izq);
  //  delay(50);

    }
  } else {
    Serial.println("No se pudo solicitar datos.");
          // direc.write(centro);

  }





  
}
