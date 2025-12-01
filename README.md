/*
แหล่งอ้างอิง https://www.ab.in.th/b/26
*/

#include <ESP8266WiFi.h>
#include <WiFiUDP.h>

int status = WL_IDLE_STATUS;
const char* ssid = "9arduino";  //  your network SSID (name)
const char* pass = "Pass wifi";       // your network password
unsigned int localPort = 2390;      // Port สำหรับการเชื่อมต่อ
byte packetBuffer[512]; //buffer to hold incoming and outgoing packets
// A UDP instance to let us send and receive packets over UDP
WiFiUDP Udp;

int a;
int b;
int c;
int d;
int pw1;
int pw2;

const int pin1 = D1;
const int pin2 = D2;
const int pin3 = D3;
const int pin4 = D4;
const int pwm1 = D5;
const int pwm2 = D6;

void setup()
{
  // Open serial communications and wait for port to open:
  Serial.begin(115200);

  pinMode(pin1, OUTPUT);
  pinMode(pin2, OUTPUT);
  pinMode(pin3, OUTPUT);
  pinMode(pin4, OUTPUT);
  pinMode(pwm1, OUTPUT);
  pinMode(pwm2, OUTPUT);

        digitalWrite(pin1, HIGH);
        digitalWrite(pin2, HIGH);
        digitalWrite(pin3, HIGH);
        digitalWrite(pin4, HIGH);

  while (!Serial) {
    ; // wait for serial port to connect. Needed for Leonardo only
  }

  // setting up Station AP
  WiFi.begin(ssid, pass);

  // Wait for connect to AP
  Serial.print("[Connecting]");
  Serial.print(ssid);
  int tries=0;
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
    tries++;
    if (tries > 30){
      break;
    }
  }
  
  Serial.println();
printWifiStatus();
  Serial.println("Connected to wifi");
  Serial.print("Udp server started at port ");
  Serial.println(localPort);
  Udp.begin(localPort);
}

void loop()
{
  int noBytes = Udp.parsePacket();
  if ( noBytes ) {
    Serial.print(millis() / 1000);
    Serial.print(":Packet of ");
    Serial.print(noBytes);
    Serial.print(" received from ");
    Serial.print(Udp.remoteIP());
    Serial.print(":");
    Serial.println(Udp.remotePort());

    // We've received a packet, read the data from it
    Udp.read(packetBuffer,noBytes); // read the packet into the buffer

/* motor 1 */
      Serial.print("Motor 1 ");
      Serial.print(packetBuffer[2],DEC);
      Serial.print(" ");
      Serial.print(packetBuffer[3],DEC);
      Serial.print(" ");
      a = packetBuffer[2],DEC;
      b = packetBuffer[3],DEC;
      if (a == 255 and b >= 106){     //เลี้ยวซ้าย
        Serial.print(" Left ");
        pw1 = map(b, 255, 106, 0, 255);
        digitalWrite(pin1, LOW);
        digitalWrite(pin2, HIGH);
        }

      else if(a == 0 and b <= 150 and b >= 1){ //เลี้ยวขวา
        Serial.print(" Right ");
        pw1 = map(b, 1, 150, 0, 255);
        digitalWrite(pin2, LOW);
        digitalWrite(pin1, HIGH);
        }

      else{
        Serial.print(" Stop ");
        pw1 = 0;
        digitalWrite(pin1, HIGH);
        digitalWrite(pin2, HIGH);
        }
    Serial.println(pw1);

/* motor 2 */
      Serial.print("Motor 2 ");
      Serial.print(packetBuffer[6],DEC);
      Serial.print(" ");
      Serial.print(packetBuffer[7],DEC);
      Serial.print(" ");
      c = packetBuffer[6],DEC;
      d = packetBuffer[7],DEC;
      if (c == 255 and d >= 106){     //ถอยหลัง
        Serial.print(" BACK ");
        pw2 = map(d, 255, 106, 0, 255);
        digitalWrite(pin4, LOW);
        digitalWrite(pin3, HIGH);
        }

      else if(c == 0 and d <= 150 and d >= 1){    //เดินหน้า
        Serial.print(" GO ");
        pw2 = map(d, 1, 150, 0, 255);
        digitalWrite(pin3, LOW);
        digitalWrite(pin4, HIGH);
        }

      else{
        Serial.print(" stop ");
        pw2 = 0;
        digitalWrite(pin3, HIGH);
        digitalWrite(pin4, HIGH);
        }

      Serial.print(pw2);
    Serial.println();
  } // end if

}

void printWifiStatus() {
  // print the SSID of the network you're attached to:
  Serial.print("SSID: ");
  Serial.println(WiFi.SSID());
  // print your WiFi shield's IP address:
  IPAddress ip = WiFi.localIP();
  Serial.print("IP Address: ");
  Serial.println(ip);
}
