<img width="879" height="760" alt="circuitbyme" src="https://github.com/user-attachments/assets/d4af2556-62be-425b-8bf9-ffed156948a4" />
[komunikacja_projekt.pdf](https://github.com/user-attachments/files/23888727/komunikacja_projekt.pdf)
#define trigPin 7
#define echoPin 6

// LEDY
#define led_orange1 13   // pomarańczowy
#define led_orange2 12   // pomarańczowy
#define led_green1  11   // zielony
#define led_green2  10   // zielony
#define led_red1    9    // czerwony
#define led_red2    8    // czerwony

#define buzzer 3

long duration;
float distance_cm;
bool buzzerOn = false;

void setup() {
  Serial.begin(9600);

  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);

  pinMode(led_orange1, OUTPUT);
  pinMode(led_orange2, OUTPUT);
  pinMode(led_green1, OUTPUT);
  pinMode(led_green2, OUTPUT);
  pinMode(led_red1, OUTPUT);
  pinMode(led_red2, OUTPUT);

  pinMode(buzzer, OUTPUT);
}

void loop() {

  // --- pomiar odległości ---
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);

  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);

  duration = pulseIn(echoPin, HIGH, 30000); // timeout 30 ms
  distance_cm = (duration / 2.0) / 29.1;    // w cm

  // najpierw wszystko gasimy
  digitalWrite(led_orange1, LOW);
  digitalWrite(led_orange2, LOW);
  digitalWrite(led_green1, LOW);
  digitalWrite(led_green2, LOW);
  digitalWrite(led_red1, LOW);
  digitalWrite(led_red2, LOW);
  noTone(buzzer);
  buzzerOn = false;

  // obsługa, gdy nie ma pomiaru (brak echa)
  if (duration == 0 || distance_cm <= 0) {
    // brak pomiaru -> wysyłamy distance = -1
    distance_cm = -1;

    // traktujemy jak bezpieczną odległość -> świecą zielone
    digitalWrite(led_green1, HIGH);
    digitalWrite(led_green2, HIGH);
  }
  else {
    // ---- STREFY ----
    if (distance_cm <= 5.0) {
      // CZERWONA strefa: <= 5 cm
      digitalWrite(led_red1, HIGH);
      digitalWrite(led_red2, HIGH);

      tone(buzzer, 3500);
      buzzerOn = true;
    }
    else if (distance_cm > 5.0 && distance_cm <= 15.0) {
      // POMARAŃCZOWA strefa: 5–15 cm
      digitalWrite(led_orange1, HIGH);
      digitalWrite(led_orange2, HIGH);

      tone(buzzer, 2000);
      buzzerOn = true;
    }
    else {
      // ZIELONA strefa: > 15 cm
      digitalWrite(led_green1, HIGH);
      digitalWrite(led_green2, HIGH);

      noTone(buzzer);
      buzzerOn = false;
    }
  }

  // --- ODCZYT STANÓW LED I WYSŁANIE JSONA ---
  int lo1 = digitalRead(led_orange1);
  int lo2 = digitalRead(led_orange2);
  int lg1 = digitalRead(led_green1);
  int lg2 = digitalRead(led_green2);
  int lr1 = digitalRead(led_red1);
  int lr2 = digitalRead(led_red2);

  Serial.print("{\"distance\":");
  Serial.print(distance_cm, 2);  // 2 miejsca po przecinku
  Serial.print(",\"led_orange1\":");
  Serial.print(lo1);
  Serial.print(",\"led_orange2\":");
  Serial.print(lo2);
  Serial.print(",\"led_green1\":");
  Serial.print(lg1);
  Serial.print(",\"led_green2\":");
  Serial.print(lg2);
  Serial.print(",\"led_red1\":");
  Serial.print(lr1);
  Serial.print(",\"led_red2\":");
  Serial.print(lr2);
  Serial.print(",\"buzzer\":");
  Serial.print(buzzerOn ? 1 : 0);
  Serial.println("}");

  delay(100);
}

