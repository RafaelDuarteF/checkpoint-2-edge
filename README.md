# Projeto sensor de Iluminosidade (Checkpoint Edge)

## Equipe

Rafael Duarte, RM 558644 <br>
Rafael Gaspar, RM 557228 <br> 
Vinicius Monteiro, RM 555088 <br>
Guilherme Oliveira, RM 555180 <br> 
Enzo Diógenes, RM 555062 <br>
Victor Mesquita, RM 558258 <br>

## Link da simulação em TinkerCad
https://www.tinkercad.com/things/gx1TBISQgtq-checkpoint-edge-2

## Dependências:
1. Buzzer
2. Sensor de iluminosidade
3. 3x leds (um vermelho, um amarelo, um verde)
4. 6x Resistores, 4x de 1KΩ e 2x de 220Ω
5. Sensor de Umidade
6. Sensor de Temperatura
7. Tela LCD
8. Conectores macho / fêmea e macho / macho

## Como reproduzi-lo
Enquanto a luminosidade for muito alta, o led vermelho será acionado juntamente com o buzzer para emitir sons de alerta, e a mensagem de "Luminosidade alta" será exibida no LCD. <br>
Enquanto for média, o led amarelo estará aceso demonstrando um sinal neutro, e a mensagem de "Ambiente a meia luz" será exibida no LCD.<br>
Enquanto for baixa, o led verde se manterá acionado demonstrando luminosidade adequada. <br>
Programe os leds para acender nesses momentos, juntamente com o buzzer no momento da led vermelha. Você define qual o valor de luminosidade é adequado, qual é médio e qual é muito elevado, e considerar esses valores para essa programação. Ex: verde = 0 a 329; amarelo = 330 a 549 e vermelho = maior ou igual a 550. 

Para os sensores de umidade e temperatura, o processo é o mesmo. É necessário definir valores que são considerados adequado, baixo e alto. Caso o valor esteja correto no momento da verificação de algum dos dois, ligar o led verde e exibir no LCD "Umidade OK" ou "Temperatura OK", abaixo os valores respectivos a ele. Caso contrário, exibir "Umidade alta", "Umidade baixa", "Temperatura alta" ou "Temperatura baixa" dependendo do caso, ligando o led amarelo para temperatura inadequada e vermelho para umidade inadequada.

### Reprodução visual:

![image](https://github.com/RafaelDuarteF/checkpoint-2-edge/assets/103393497/e44cdb93-9335-44a1-a595-13b510383028)

### Código utilizado:

```C++
#include <LiquidCrystal.h>
LiquidCrystal lcd(7, 6, 5, 4, 3, 2, 12);

const int ledPinRed = 10;  
const int ledPinYellow = 9;  
const int ledPinGreen = 8;  
const int ldrPin = A0;  
const int BUZZER_PIN = 11;

const int analogIn = A2;
int humiditysensorOutput = 0;
int RawValue= 0;
double Voltage = 0;
int tempC = 0;
int humidity = 0;

void setup() {
  Serial.begin(9600);
  lcd.begin(16, 2);
  pinMode(ledPinRed, OUTPUT);  
  pinMode(ledPinYellow, OUTPUT);  
  pinMode(ledPinGreen, OUTPUT);  
  pinMode(ldrPin, INPUT);
  pinMode(A1, INPUT);
}

void loop() {
  int ldrStatus = analogRead(ldrPin);   

  if (ldrStatus >= 0 && ldrStatus < 330) {
	
    digitalWrite(ledPinGreen, HIGH); 
    digitalWrite(ledPinRed, LOW); 
    digitalWrite(ledPinYellow, LOW); 
    noTone(BUZZER_PIN);  // Para o som
    lcd.clear();
    lcd.setCursor(1, 0);
    lcd.print("Luminosidade OK");
    delay(5000);

  }

  else if (ldrStatus >=  330 && ldrStatus < 550) {

    digitalWrite(ledPinYellow, HIGH);
    digitalWrite(ledPinGreen, LOW); 
    digitalWrite(ledPinRed, LOW); 
    noTone(BUZZER_PIN);  // Para o som
	lcd.clear();
    lcd.setCursor(1, 0);
    lcd.print("Ambiente a meia");
    lcd.setCursor(6, 1);
    lcd.print("luz.");
    delay(5000);

  }

  else if (ldrStatus >= 550) {
    lcd.clear();
	lcd.setCursor(1, 0);
    lcd.print("Ambiente muito");
    lcd.setCursor(5, 1);
    lcd.print("claro.");

    digitalWrite(ledPinRed, HIGH);        
    digitalWrite(ledPinGreen, LOW); 
    digitalWrite(ledPinYellow, LOW); 

    tone(BUZZER_PIN, 1000, 200);  // Emite um som no buzzer
    delay(500);
    noTone(BUZZER_PIN);  // Para o som


    tone(BUZZER_PIN, 1000, 200);  // Emite um som no buzzer
    delay(500);
    noTone(BUZZER_PIN);  // Para o som

    tone(BUZZER_PIN, 1000, 200);  // Emite um som no buzzer
    delay(500);
    noTone(BUZZER_PIN);  // Para o som
    delay(3000);
    

  }
  
  // Umidade e Temperatura
  
  RawValue = analogRead(analogIn);
  Voltage = (RawValue / 1023.0) * 5000; // 5000 to get millivots.
  tempC = (Voltage-500) * 0.1; // 500 is the offset
  humiditysensorOutput = analogRead(A1);
  humidity = map(humiditysensorOutput, 0, 1023, 10, 70);

  if(tempC >= 10 && tempC <= 15) {
	lcd.clear();
	lcd.setCursor(1, 0);
    lcd.print("Temperatura OK");
    lcd.setCursor(1, 1);
    lcd.print("Temp. = ");
    lcd.print(tempC);
    lcd.print("C");
    digitalWrite(ledPinYellow, LOW);
    digitalWrite(ledPinGreen, HIGH); 
    digitalWrite(ledPinRed, LOW); 
    delay(5000);
  } else if(tempC > 15) {
  	lcd.clear();
	lcd.setCursor(0, 0);
    lcd.print("Temperatura Alta");
    lcd.setCursor(1, 1);
    lcd.print("Temp. = ");
    lcd.print(tempC);
    lcd.print("C");
    digitalWrite(ledPinYellow, HIGH);
    digitalWrite(ledPinGreen, LOW); 
    digitalWrite(ledPinRed, LOW); 
    
    tone(BUZZER_PIN, 1000, 200);  // Emite um som no buzzer
    delay(1000);
    noTone(BUZZER_PIN);  // Para o som


    tone(BUZZER_PIN, 1000, 200);  // Emite um som no buzzer
    delay(1000);
    noTone(BUZZER_PIN);  // Para o som

    tone(BUZZER_PIN, 1000, 200);  // Emite um som no buzzer
    delay(1000);
    noTone(BUZZER_PIN);  // Para o som
    delay(3000);
  } else {
    lcd.clear();
	lcd.setCursor(3, 0);
    lcd.print("Temp. Baixa");
    lcd.setCursor(1, 1);
    lcd.print("Temp. = ");
    lcd.print(tempC);
    lcd.print("C");
    digitalWrite(ledPinYellow, HIGH);
    digitalWrite(ledPinGreen, LOW); 
    digitalWrite(ledPinRed, LOW); 
    tone(BUZZER_PIN, 1000, 200);  // Emite um som no buzzer
    delay(1000);
    noTone(BUZZER_PIN);  // Para o som


    tone(BUZZER_PIN, 1000, 200);  // Emite um som no buzzer
    delay(1000);
    noTone(BUZZER_PIN);  // Para o som

    tone(BUZZER_PIN, 1000, 200);  // Emite um som no buzzer
    delay(1000);
    noTone(BUZZER_PIN);  // Para o som
    delay(3000);
  }
  
  
  
  if(humidity >= 50 && humidity <= 70) {
  	lcd.clear();
	lcd.setCursor(3, 0);
    lcd.print("Umidade OK");
    lcd.setCursor(2, 1);
    lcd.print("Umidade: ");
    lcd.print(humidity); // Aqui estamos imprimindo a variável humidityPercentage
    lcd.print("%");
    digitalWrite(ledPinYellow, LOW);
    digitalWrite(ledPinGreen, HIGH); 
    digitalWrite(ledPinRed, LOW); 
    delay(5000);
  } else if(humidity > 70) {
    lcd.clear();
	lcd.setCursor(1, 0);
    lcd.print("Umidade Alta");
    lcd.setCursor(2, 1);
    lcd.print("Umidade: ");
    lcd.print(humidity); // Aqui estamos imprimindo a variável humidityPercentage
    lcd.print("%");
    digitalWrite(ledPinYellow, LOW);
    digitalWrite(ledPinGreen, LOW); 
    digitalWrite(ledPinRed, HIGH); 
    tone(BUZZER_PIN, 1000, 200);  // Emite um som no buzzer
    delay(500);
    noTone(BUZZER_PIN);  // Para o som


    tone(BUZZER_PIN, 1000, 200);  // Emite um som no buzzer
    delay(500);
    noTone(BUZZER_PIN);  // Para o som

    tone(BUZZER_PIN, 1000, 200);  // Emite um som no buzzer
    delay(500);
    noTone(BUZZER_PIN);  // Para o som
    delay(3000);
  } else {
    lcd.clear();
	lcd.setCursor(1, 0);
    lcd.print("Umidade Baixa");
    lcd.setCursor(2, 1);
    lcd.print("Umidade: ");
    lcd.print(humidity); // Aqui estamos imprimindo a variável humidityPercentage
    lcd.print("%");
    digitalWrite(ledPinYellow, LOW);
    digitalWrite(ledPinGreen, LOW); 
    digitalWrite(ledPinRed, HIGH); 
    tone(BUZZER_PIN, 1000, 200);  // Emite um som no buzzer
    delay(500);
    noTone(BUZZER_PIN);  // Para o som


    tone(BUZZER_PIN, 1000, 200);  // Emite um som no buzzer
    delay(500);
    noTone(BUZZER_PIN);  // Para o som

    tone(BUZZER_PIN, 1000, 200);  // Emite um som no buzzer
    delay(500);
    noTone(BUZZER_PIN);  // Para o som
    delay(3000);
  }

}
```

