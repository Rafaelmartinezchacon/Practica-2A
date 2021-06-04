
# Practica A: Interupción por GPIO



```
#include <Arduino.h>
struct Button {
const uint8_t PIN;
uint32_t numberKeyPresses;
bool pressed;
};
Button button1 = {0, 0, false};
void IRAM_ATTR isr() {
button1.numberKeyPresses += 1;
button1.pressed = true;
}
void setup() {
Serial.begin(9600);
pinMode(button1.PIN, INPUT_PULLUP);
attachInterrupt(button1.PIN, isr, FALLING);
}
void loop() {
if (button1.pressed) {
Serial.printf("Button 1 has been pressed %u times\n", button1.numberKeyPresses);
button1.pressed = false;
}
//Detach Interrupt after 1 Minute
static uint32_t lastMillis = 0;
if (millis() - lastMillis > 60000) {
lastMillis = millis();
detachInterrupt(button1.PIN);
Serial.println("Interrupt Detached!");
}
}
```

#Funcionaminento de esta practica

Para hacer esta parte de la practica necesitamos utilizar la función `attachInterrupt(GPIOPin, isr, Mode);`. Con esta función lo que conseguiremos es una interrupción en base a un pin por pin.

```
void setup() {
Serial.begin(9600);
pinMode(button1.PIN, INPUT_PULLUP);
attachInterrupt(button1.PIN, isr, FALLING);
}
```

En el apartado de button.pin establecemos la clavija GPIO que usaremos para su interrupción. En Falling señalamos que los disparadores interrumpen cuando el pin va de High a Low. En isr llamos a la rutina de interrupción con el siguiente codigo:

```void IRAM_ATTR isr() {
button1.numberKeyPresses += 1;
button1.pressed = true;
}
```

Una vez llamada el isr, creamos un loop donde cada vez que se pulse el button1, el monitos nos mostrara por pantalla `"Button 1 has been pressed %u times\n"`. En la parte que pone %u, pondremos las veces que se pulsado el boton. Seguidamente del loop tenemos el siguiente codigo:

```
static uint32_t lastMillis = 0;
if (millis() - lastMillis > 60000) {
lastMillis = millis();
detachInterrupt(button1.PIN);
Serial.println("Interrupt Detached!");
}
```

El funcionamiento de este ultimo codigo trata sobre que una vez llegue a pasar 1 minuto, de forma automatica desconectara la interrupción GPIO que tenemos asignada.

