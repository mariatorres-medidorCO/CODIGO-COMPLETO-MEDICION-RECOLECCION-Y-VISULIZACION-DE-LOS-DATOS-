# CODIGO-COMPLETO-MEDICION-RECOLECCION-Y-VISULIZACION-DE-LOS-DATOS-
Esperamos sea de su agrado nuestro proyecto
#include <SD.h>
#include <SPI.h>

const int analogPin=A0;
int sensorMq=0; 
int sensorlimite = 30;

File myFile; 

  int segundos=0;
  int minutos=0;
  int horas=0;
  int dia=0;
void setup()
{
  Serial.begin(9600);

   pinMode(4, OUTPUT); // LED
   pinMode(9, INPUT); // Valor digital sensor
   pinMode(2, OUTPUT);//LED
   pinMode(5, OUTPUT);
   Serial.begin(9600);   
  Serial.begin(9600);
  
  Serial.print("Iniciando SD ...");
  if (!SD.begin(10)) {
    Serial.println("No se pudo inicializar");
    return;
  }
  Serial.println("inicializacion exitosa");
  
  if(!SD.exists("prueba3.csv"))
  {
      myFile = SD.open("prueba3.csv", FILE_WRITE);
      if (myFile) {
        segundos=segundos+2;
          
          if (segundos>=60){
              segundos=segundos-60;
              minutos=minutos+1;
              if(minutos>=60){
                    minutos=minutos-60;
                    horas=horas+1;
                    if(horas>=24){
                          dia=dia+1;
                          horas=horas-24;
                       }
                } 
          }    
          myFile.print("Tiempo = dia ");
          myFile.print(dia);
          myFile.print(", ");
          if(horas<10)
          {
            myFile.print("0");
            }
          myFile.print(horas);
          myFile.print(":");
          if(minutos<10)
          {
            myFile.print("0");
            }
          myFile.print(minutos);
          myFile.print(":");
          if(segundos<10)
          {
            myFile.print("0");
            }
          myFile.println(segundos);
          myFile.print(", CO = ");//Temperatura, primer sensor
          myFile.print(A0);
          
          myFile.close(); //cerramos el archivo              
        Serial.println("Archivo nuevo, Escribiendo encabezado(fila 1)");
        myFile.println("Tiempo(ms),Sensor1");
        myFile.close();
      } else {

        Serial.println("Error creando el archivo datalog.csv");
      }
  }
  
}

void loop()
{
  int MQ7 = analogRead(A0); //Lemos la salida analógica del MQ
  float voltaje = MQ7 * (5.0 / 1023.0); //Convertimos la lectura en un valor de voltaje
  float Rs= 1000*((5-voltaje)/voltaje); //Calculamos Rs con un RL de 1k
  double PPM = 100*pow(Rs/2116.4, -1.513); // calculamos la concentración de CO
  //-------Enviamos los valores por el puerto serial------------
  Serial.print("Valor PPM: ");
  Serial.println(PPM);
  delay(1000);

   if (PPM > sensorlimite){ // Capturo analogRead (0) que es el valor analogico del sensor
      digitalWrite(4, HIGH); // Enciendo LED ROJO
      digitalWrite(2,LOW);}
      
   else {
        digitalWrite(4, LOW);
        digitalWrite(2, HIGH);}
  
  myFile = SD.open("prueba3.csv", FILE_WRITE);//abrimos el archivo
  
  if (myFile) { 
        Serial.print("Escribiendo SD: ");
        int sensor1 = analogRead(A0);
        myFile.print(millis());
      
        myFile.close(); //cerramos el archivo
        
        Serial.print("Tiempo(ms)=");
        Serial.print(millis());
    
        
  } else {
    // if the file didn't open, print an error:
    Serial.println("Error al abrir el archivo");
  }
  delay(1000);
}
