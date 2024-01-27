# Codigo
    #include <ArduinoJson.h>
     #include <WiFi.h>
     #include <PubSubClient.h>
     #define BUILTIN_LED 2

     #include <LiquidCrystal_I2C.h>
    #define I2C_ADDR 0x27 
    #define LCD_COLUMNS 20
    #define LCD_LINES 4
    int boton1 = 4;
    int boton2 = 5;
    int boton3 = 2;
    int estado = 0;

    LiquidCrystal_I2C lcd(I2C_ADDR, LCD_COLUMNS, LCD_LINES);

    const char* ssid = "Wokwi-GUEST";
    const char* password = "";
    const char* mqtt_server = "3.65.168.153";
    String username_mqtt="ProyectoFinal";
    String password_mqtt="12345678";

    WiFiClient espClient;
    PubSubClient client(espClient);
    unsigned long lastMsg = 0;
    #define MSG_BUFFER_SIZE  (50)
    char msg[MSG_BUFFER_SIZE];
    int value = 0;

    void setup_wifi() {

    delay(10);
    // We start by connecting to a WiFi network
    Serial.println();
    Serial.print("Connecting to ");
    Serial.println(ssid);

    WiFi.mode(WIFI_STA);
    WiFi.begin(ssid, password);

    while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
    }

    randomSeed(micros());

    Serial.println("");
    Serial.println("WiFi connected");
    Serial.println("IP address: ");
    Serial.println(WiFi.localIP());
     }

    void callback(char* topic, byte* payload, unsigned int length) {
     Serial.print("Message arrived [");
     Serial.print(topic);
     Serial.print("] ");
     for (int i = 0; i < length; i++) {
     Serial.print((char)payload[i]);
     }
    Serial.println();

     // Switch on the LED if an 1 was received as first character
    if ((char)payload[0] == '1') {
    digitalWrite(BUILTIN_LED, LOW);   
    // Turn the LED on (Note that LOW is the voltage level
    // but actually the LED is on; this is because
    // it is active low on the ESP-01)
    } else {
    digitalWrite(BUILTIN_LED, HIGH);  
    // Turn the LED off by making the voltage HIGH
    }

    }

     void reconnect() {
     // Loop until we're reconnected
    while (!client.connected()) {
    Serial.print("Attempting MQTT connection...");
    // Create a random client ID
    String clientId = "ESP8266Client-";
    clientId += String(random(0xffff), HEX);
    // Attempt to connect
    if (client.connect(clientId.c_str(), username_mqtt.c_str() , password_mqtt.c_str())) {
      Serial.println("connected");
      // Once connected, publish an announcement...
      client.publish("outTopic", "hello world");
      // ... and resubscribe
      client.subscribe("inTopic");
    } else {
      Serial.print("failed, rc=");
      Serial.print(client.state());
      Serial.println(" try again in 5 seconds");
      // Wait 5 seconds before retrying
      delay(5000);
    }
    }
    }

     void setup() {
     pinMode(BUILTIN_LED, OUTPUT);     // Initialize the BUILTIN_LED pin as an output
     Serial.begin(115200);
     setup_wifi();
    client.setServer(mqtt_server, 1883);
     client.setCallback(callback);

     pinMode(boton1, INPUT);
     lcd.init(); 
     lcd.backlight();
     pinMode(boton2, INPUT);
     lcd.init(); 
    lcd.backlight();
    pinMode(boton3, INPUT);
    lcd.init(); 
    lcd.backlight();
    }

    void loop() {
 
  
  
    if (!client.connected()) {
    reconnect();
    }
      client.loop();

     unsigned long now = millis();
    if (now - lastMsg > 2000) {
    lastMsg = now;
    //++value;
    //snprintf (msg, MSG_BUFFER_SIZE, "hello world #%ld", value);

    StaticJsonDocument<128> doc;

    if (digitalRead(boton1) == true) {
  
    lcd.clear();
    lcd.setCursor(0,0);
    lcd.print("Ing Amairani CH");
    lcd.setCursor(0,1);
    lcd.print("Edad: 27");
    delay(2000); 
 
     lcd.clear();
     lcd.setCursor(0,0);
     lcd.print("Peso:65kg");
     lcd.setCursor(0,1);
     lcd.print("Estatura:162cm");
     delay(2000);

     lcd.clear();

    doc["NOMBRE"]="ING Amairani CH";
    doc["EDAD"] = "27";
    doc["PESO"] = "65kg";
    doc["ESTATURA"] = "162cm";
      }

      if (digitalRead(boton2) == true) {
     lcd.clear();
     lcd.setCursor(0,0);
     lcd.print("Ing Armando GL");
     lcd.setCursor(0,1);
     lcd.print("Edad: 25 ");
     delay(2000);

     lcd.clear();
     lcd.setCursor(0,0);
     lcd.print("Peso:70kg");
     lcd.setCursor(0,1);
     lcd.print("Estatura:170cm");
    delay(2000);
    lcd.clear();
    doc["NOMBRE"]="ING Armando";
    doc["EDAD"] = "25";
    doc["PESO"] ="70kg";
    doc["ESTATURA"] = "170cm";
      }
 
      if (digitalRead(boton3) == true) {
     lcd.clear();
     lcd.setCursor(0,0);
     lcd.print("Ing Adalberto GL");
     lcd.setCursor(0,1);
     lcd.print("Edad: 25  ");
     delay(2000);

     lcd.clear();
     lcd.setCursor(0,0);
     lcd.print("Peso:89 kg");
     lcd.setCursor(0,1);
     lcd.print("Estatura:170cm");
    delay(2000);
    lcd.clear();
    doc["NOMBRE"]="ING Adalberto GL";
    doc["EDAD"] = "25";
    doc["PESO"] ="89kg";
    doc["ESTATURA"] = "170cm";
      }
      if(digitalRead(boton3)==false && digitalRead(boton2)==false && digitalRead(boton1)==false ){
     lcd.print("Pulse un boton");
     lcd.setCursor(0, 0); 
     delay(2000); 
     lcd.clear();    
   
    String output;
    
    serializeJson(doc, output);

    Serial.print("Publish message: ");
    Serial.println(output);
    Serial.println(output.c_str());
    client.publish("ProyectoFinal", output.c_str());
  }
}
}
