
Sessioni
- part 1:  intro Arduino (1 Marzo 2023)
- part 2: I2C + Cloud 



## 1 Marzo 2023
 - materiale per 3 gg  + 2 gg di progettino
 

`
## Story 
Arduino: nasce come strumenteo per insegnare interaction 
design.

Loop of the interaction design:
- design
- build
- repeate

Chiudere il lopp in manierea facile è lo scopo iniziale di arduino con una scheda semplice da programmare con un ambiente di sviluppo intuitivo.


Processing (https://processing.org/) è un linguaggio di progrmmazione che nasce nel MIT che nasce per insegnare a programmare agli interaction design.
Arduino è inspirato da Processing ma collegato all'hardware.



- Sensors
- microntroller 
	- *AVR* : e uno dei primo nasce da un progettto niversitario nelgli anni 90.Rese standarad l'utilizzo della memeoria flash  inel processore. Avere una memorua flash nel microprocessore permette di riprogrammare il controllore più  volte.
- Actuators

Arduino IDE
- facilitare 

### Arduino starter KIT
- è basato su 15 progetti dove la scatola ( basata su 400 prototipi :) 

### board : Arduino uno R3
in fase di creazione della board viene flashato un firmware che fa lampeggiare la luce verde.
- usb: è stata una rivoluzione perchè gli altri avevano la seriale 

> [!definitino] Time to blink
> il tempo che passa da quando prendi la scheda e fai blinkare un  led

- core arduino: un libreria in C++ che astre dal processore.  IN arduino la funzione main ``#inclde arduino ` 


### Tutorial steps 

#### Connect led to arudino board
1. connect the first led (red): piedino corto su GND e lungo su pin 13.

#### First sketch

```
void setup() {
  pinMode(LED_BUILTIN, OUTPUT);// initialize digital pin LED_BUILTIN as an output.
}

  

// the loop function runs over and over again forever

void loop() {
	digitalWrite(LED_BUILTIN, HIGH); // turn the LED on (HIGH is the voltage level)
	delay(1000); // wait for a second
	digitalWrite(LED_BUILTIN, LOW); // turn the LED off by making the voltage LOW
	delay(1000); // wait for a second
}
```


Spiegazione cose fonadmentali:
- OUTPUT e LED_BUILTIN: sono macro definite da arduino
- pinMode cosa fa
- digitalWrite é una funzione che fa parte dell'alfabeto di arduino
- delay: mette a riposo la scheda.


> [!definition] Alfabeto di arduino
> Sono un set comandi che sono fondamentali che puoi fare "tutto" e sono comandi ortogonali (`analogRead` and `analogWrite`)


Steps:
 - modifiare il delay da 400 a 3000
 - autodetect: notare che la scheda viene riconosciuta in automatico (cosa molto innovativa)

Notare
- la `digitalWrite` è il primo esempio di collegamenteo tra il modo software e l'hardware.

Next
- usare le costanti `const` per definire 


``` c
const in LED = 13;

void setup() {
  pinMode(LED_BUILTIN, OUTPUT);// initialize digital pin LED_BUILTIN as an output.
}

void loop() {
	digitalWrite(LED_BUILTIN, HIGH); // turn the LED on (HIGH is the voltage level)
	delay(1000); // wait for a second
	digitalWrite(LED_BUILTIN, LOW); // turn the LED off by making the voltage LOW
	delay(1000); // wait for a second
}
```


Questions: 
 - What happens if delay is very small ?? rimane sempre accesso
 - what happens if delays are differente (say 3 and 7) ? la percezione è che il led è sempre accesso ma è meno luminoso.
	 - si introduce il concetto di `PWM` . La differenza tra tutto il period e il duty (accesso).  La percentuale dil duty cycle ci dice la luminosita. che sfrutta la pesistenza dell'immagine dell'occhio. quindi accendendo e spegnendo un led cont tempi diversi, la percezione è che la luminosità è inferiore.

  #### First circuit 

- *breadboard*: il vnataggio   è che posso collegare i componenti tra di loto in modo facile sperimentando.
	- Il nome vero è solderless breadboard 

- *resistenza*: è un componente che "resiste" il flusso di corrente.  
	- attaccare la resistenza al gmabo corto del led.
	- 
``` c
const int pinLed = 9;

void setup() {
  pinMode(pinLed, OUTPUT);
}

void loop() {
  digitalWrite(pinLed, HIGH); // turn the LED on (HIGH is the voltage level)
  delay(1000); // wait for a second
  digitalWrite(pinLed, LOW); // turn the LED off by making the voltage LOW
  delay(1000); // wait for a second
}
```


Esercizio
- Fare "respirare" il led con un fead in and fead out.
```c
/ the setup function runs once when you press reset or power the board

void setup() {

// initialize digital pin LED_BUILTIN as an output.

pinMode(LED, OUTPUT);

}

  

int fadeValue = 0; // max 255 values

int increase = 1;

void loop() {

analogWrite(pinLed, fadeValue); // turn the LED on (HIGH is the voltage level)

delay(30);

  

if (increase == 1){

fadeValue = fadeValue + 5;

}else{

fadeValue = fadeValue + 5;

}

if (fadeValue >= 255){

increase = 0;

}

if (fadeValue <= 0){

increase = 1;

}

  

}
```

#### Button and led


- `Corrente`:Spiegazione della corrente eletrrica come se fosee dell'acqua che passa nel tubo (fili).
- circuito:

``` c
const int LED = 13;
const int BUTTON = 7;
int val = 0;

void setup() {
pinMode(LED, OUTPUT);
pinMode(BUTTON, INPUT); // the BUTTON is an input
}

void loop() {
	val = digitalRead(BUTTON);
	// TODO: mettere delay(100) . per sperimentare di quanti millisecondi devono passare per rendere frustrante l'interazione con l'hardware
	if (val == HIGH){
		digitalWrite(LED, HIGH);
	}else{
		digitalWrite(LED, LOW);
	}
}
```

Questions:
- // TODO: mettere delay(100) prima dellif? . per sperimentare di quanti millisecondi devono passare per rendere frustrante l'interazione con l'hardware 
- togliere la resistenza, cosa succede ? se tocco i fili che va al in 7 il led si accede. perché ? come succede con gli amplificari audio, che se i piedini di ingresso di arduino sono sensibili che sono `floating` che 

Extensions
- metter un sensore di luce (connettere corto su resistenza e lunga su 5v), si ottiene un sesnosere di luce che accende il led.
- `analogRead`: connettere il pin collegato al ping lungo al pin A0. 
 
```c
int val = 0;

void setup() {
	Serial.begin(9200);
}

void loop() {
	val = analogRead(0);
	Serial.println(val);
	delay(100);
}
```


Idee
- fare un sensore di pressione con una spugna e due fili infilati dentro. pigiando la spugna la resistenza diminuisce.

##### Servo motore tamite analogRead
Example /servo/sweap


```c
#include <Servo.h>
int val = 0;
const int sensorPin = 0;
const int servoPin = 9;

Servo s;

void setup() {
	Serial.begin(9200);
	s.attach(servoPin);
}

void loop() {
	val = analogRead(0);
	Serial.print(val);
	Serial.print(",");
	// il servo motore accetta solo i valori: 0-179
	// mi aspetto un moero tra `1,1023] e viene mappato in [0,255]
	val = map(val, 0, 1023, 0, 255);
	val = constrain(val, 0, 180);
	s.write(val);
	Serial.println(val);
}
```

note on functon
- `map`
- `constraint` 

Esercizio
- fare un esercizio che usi: sensore si luce, servo motore che faccia qualcosa. Eg. un robottino che se gli metti davanti la mano prende paura.


*Serial communication*


#### Multple leds



Exercise.
 - fare il gioco del [simon](https://it.wikipedia.org/wiki/Simon_(gioco)): 4 pulsanti, 4 led e il cicalino. 