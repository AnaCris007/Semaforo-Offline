# Semáforo (projeto com Arduino)

Projeto de semáforo simples usando Arduino e um display LCD I2C 16x2. O sistema simula as fases de um semáforo (vermelho, amarelo e verde) acionando LEDs e exibindo mensagens no LCD. Há também um vídeo demonstrativo (link a ser adicionado posteriormente).

## Descrição

Este projeto implementa um semáforo básico conectado a um Arduino. O comportamento segue um ciclo: vermelho -> amarelo -> verde -> (verde estendido) -> amarelo -> reinício. Em cada fase os LEDs correspondentes acendem e o display LCD exibe uma mensagem indicando o estado.

## Componentes usados

- Arduino Uno (ou compatível)
- 1 x LCD I2C 16x2 (endereço típico 0x27)
- 3 x LEDs (vermelho, amarelo, verde)
- 3 x resistores 220 Ω (para LEDs)
- Jumpers e protoboard
- Fonte USB para o Arduino

## Ligação / Fiação (mapa de pinos)

- LED Vermelho: pino digital 10 -> resistor 220Ω -> ânodo LED -> cátodo -> GND
- LED Amarelo: pino digital 9  -> resistor 220Ω -> ânodo LED -> cátodo -> GND
- LED Verde: pino digital 8   -> resistor 220Ω -> ânodo LED -> cátodo -> GND
- LCD I2C: VCC -> 5V, GND -> GND, SDA -> A4 (no Uno), SCL -> A5 (no Uno)
	- Endereço I2C usado no código: 0x27

Observação: em placas diferentes do Uno (ex.: Mega, Nano Every) os pinos SDA/SCL podem variar; verifique o manual da sua placa.

## Explicação do código

O sketch (abaixo) realiza o seguinte:

- Inclui a biblioteca Wire e LiquidCrystal_I2C e inicializa o objeto `lcd` com o endereço 0x27 e dimensões 16x2.
- Define constantes para os pinos dos LEDs (10, 9 e 8).
- Em `setup()` configura os pinos como saída e inicializa o display, mostrando uma mensagem de inicialização.
- Em `loop()` executa o ciclo do semáforo:
	- Fase Vermelha: acende LED vermelho, mostra "SINAL VERMELHO / PARE!" por 6s
	- Fase Amarela: acende LED amarelo, mostra "SINAL AMARELO / Atencao!" por 2s
	- Fase Verde: acende LED verde, mostra "SINAL VERDE / Acelere" por 2s
	- Tempo adicional no verde: atualiza mensagem do LCD por mais 2s
	- Volta ao amarelo para preparar a parada por 2s
	- O ciclo se repete automaticamente

Função auxiliar `mostrarMensagem(String linha1, String linha2)` atualiza o LCD com duas linhas de texto.

Edge cases / dicas rápidas:
- Se o LCD não apresentar caracteres, verifique o endereço I2C (use um scanner I2C se necessário).
- Ajuste os delays (durações) conforme desejado para simular tempos diferentes.

## Instalação e upload

1. Abra o Arduino IDE.
2. Instale a biblioteca `LiquidCrystal_I2C` (Library Manager > procurar por "LiquidCrystal I2C" e instalar uma implementação compatível).
3. Conecte seu Arduino ao computador e selecione a placa e porta corretas no IDE.
4. Cole o código do sketch (abaixo) e faça o upload.

## Código (sketch Arduino)

```cpp
#include <Wire.h>
#include <LiquidCrystal_I2C.h>

LiquidCrystal_I2C lcd(0x27, 16, 2);

// Definição dos pinos dos LEDs
const int ledVermelho = 10;
const int ledAmarelo  = 9;
const int ledVerde    = 8;

void setup() {
	// Inicializa os LEDs
	pinMode(ledVermelho, OUTPUT);
	pinMode(ledAmarelo, OUTPUT);
	pinMode(ledVerde, OUTPUT);

	// Inicializa o LCD
	lcd.init();
	lcd.backlight();

	lcd.setCursor(0, 0);
	lcd.print("   Semaforo   ");
	lcd.setCursor(0, 1);
	lcd.print("Inicializando...");
	delay(2000);
	lcd.clear();
}

void loop() {
	// --- Fase Vermelha (6 segundos) ---
	digitalWrite(ledVermelho, HIGH);
	digitalWrite(ledAmarelo, LOW);
	digitalWrite(ledVerde, LOW);
	mostrarMensagem("SINAL VERMELHO", "PARE!");
	delay(6000);

	// --- Fase Amarela (2 segundos) ---
	digitalWrite(ledVermelho, LOW);
	digitalWrite(ledAmarelo, HIGH);
	digitalWrite(ledVerde, LOW);
	mostrarMensagem("SINAL AMARELO", "Atencao!");
	delay(2000);

	// --- Fase Verde (2 segundos) ---
	digitalWrite(ledVermelho, LOW);
	digitalWrite(ledAmarelo, LOW);
	digitalWrite(ledVerde, HIGH);
	mostrarMensagem("SINAL VERDE", "Acelere");
	delay(2000);

	// --- Tempo adicional no verde (2 segundos) ---
	mostrarMensagem("SINAL VERDE", "Vai rapido!");
	delay(2000);

	// --- Fase Amarela novamente (2 segundos) ---
	digitalWrite(ledVermelho, LOW);
	digitalWrite(ledAmarelo, HIGH);
	digitalWrite(ledVerde, LOW);
	mostrarMensagem("SINAL AMARELO", "Preparar parada");
	delay(2000);

	// Reinicia o ciclo automaticamente
}

// Função auxiliar para atualizar o LCD
void mostrarMensagem(String linha1, String linha2) {
	lcd.clear();
	lcd.setCursor(0, 0);
	lcd.print(linha1);
	lcd.setCursor(0, 1);
	lcd.print(linha2);
}
```

## Vídeo demonstrativo



