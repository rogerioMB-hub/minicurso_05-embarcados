---
layout: default
title: "Aula 1 — Primeiro Pixel: NeoPixel WS2812B"
---

# Aula 1 — Primeiro Pixel: NeoPixel WS2812B

> **Duração estimada:** 30 minutos  
> **Bloco:** 1 de 5 — Seção NeoPixel

---

## Objetivos

Ao final desta aula você será capaz de:

- Entender o que é o protocolo NeoPixel e como o LED WS2812B funciona
- Importar e configurar a biblioteca `neopixel` no MicroPython
- Endereçar LEDs individualmente usando índice
- Definir cores com tuplas `(R, G, B)`
- Enviar o sinal de atualização com `np.write()`
- Acender um LED, acender todos e apagar todos

> 💡 **Novo aqui?** Se o conceito de tupla ainda não é familiar, leia antes a [Aula 00-extra: Tuplas em Python](./aula00-extra-tuplas.md) — ela explica a sintaxe `(R, G, B)` usada em todo este curso.

---

## 1. Conceito

### O que é o WS2812B?

O **WS2812B** (também chamado de **NeoPixel**) é um LED RGB inteligente: cada unidade possui um microcontrolador embutido que recebe dados seriais e controla as três cores (vermelho, verde e azul) de forma independente.

Isso significa que com **apenas um fio de dados** você controla dezenas (ou centenas) de LEDs encadeados — cada um com sua própria cor e brilho.

### Endereçamento por índice

Os LEDs do anel são numerados de `0` a `15` (para um anel de 16 LEDs). Você acessa cada um como se fosse um elemento de lista:

```
np[0]   → primeiro LED
np[7]   → oitavo LED
np[15]  → último LED
```

### A tupla (R, G, B)

Cada cor é definida por uma **tupla de três valores**, cada um entre `0` e `255`:

| Cor | Tupla |
|-----|-------|
| Vermelho | `(255, 0, 0)` |
| Verde | `(0, 255, 0)` |
| Azul | `(0, 0, 255)` |
| Branco | `(255, 255, 255)` |
| Apagado | `(0, 0, 0)` |

> **Conexão com o que você já viu:** cada canal R, G e B é um valor de 8 bits (0 a 255). Três canais juntos formam 24 bits por LED — exatamente a largura de uma palavra de cor RGB.

### O comando np.write()

Atribuir `np[i] = cor` apenas **prepara** o dado na memória. O sinal elétrico só é enviado ao anel quando você chama:

```python
np.write()
```

Sem esse comando, nenhum LED muda de estado — não esqueça!

---

## 2. Circuito

| Componente | Quantidade |
|------------|------------|
| ESP32 DevKit | 1 |
| Anel NeoPixel WS2812B 16 LEDs | 1 |

**Conexões:**

```
ESP32 GPIO4  ──► DIN  (entrada de dados do anel)
ESP32 3.3V   ──► VCC
ESP32 GND    ──► GND
```

```
# Pico: use GPIO 0 no lugar de GPIO 4
```

> ⚠️ **Atenção:** em projetos reais com muitos LEDs em brilho máximo, use fonte externa de 5 V para o anel. No Wokwi, 3.3 V funciona normalmente.

*Link do Wokwi → [Minicurso 05 — Anel NeoPixel 16 LEDs](https://wokwi.com/projects/474715111472158721)*

---

## 3. Código

### Parte A — Acender um único LED

```python
# ============================================================
# Aula 01 — Parte A: Acender um único LED
# Mini-curso 05 — NeoPixel WS2812B
# Plataforma principal: ESP32
# ============================================================

from machine import Pin
import neopixel
import utime

# --- Configuração ---
PINO_DADOS = 4          # GPIO 4 no ESP32
# Pico: PINO_DADOS = 0  # GPIO 0 no Raspberry Pi Pico

NUM_LEDS = 16           # quantidade de LEDs no anel

# Cria o objeto NeoPixel
np = neopixel.NeoPixel(Pin(PINO_DADOS), NUM_LEDS)

# --- Acende o LED de índice 0 na cor vermelha ---
np[0] = (255, 0, 0)     # vermelho puro
np.write()              # envia o sinal para o anel — sem isso nada muda!

utime.sleep(3)          # aguarda 3 segundos

# --- Apaga o LED 0 ---
np[0] = (0, 0, 0)       # cor "apagado"
np.write()
```

> 💡 **Observe:** trocar `np[0]` por `np[8]` acende o LED do lado oposto do anel. O índice é a única diferença!

---

### Parte B — Acender todos os LEDs

```python
# ============================================================
# Aula 01 — Parte B: Acender todos os LEDs
# ============================================================

from machine import Pin
import neopixel
import utime

PINO_DADOS = 4
# Pico: PINO_DADOS = 0

NUM_LEDS = 16

np = neopixel.NeoPixel(Pin(PINO_DADOS), NUM_LEDS)

# --- Acende todos na cor azul ---
for i in range(NUM_LEDS):      # percorre índices 0 a 15
    np[i] = (0, 0, 255)        # azul puro

np.write()                     # um único write atualiza TODOS

utime.sleep(3)

# --- Apaga todos ---
for i in range(NUM_LEDS):
    np[i] = (0, 0, 0)

np.write()
```

> 💡 **Perceba:** o `np.write()` é chamado **uma única vez** após o loop — não dentro dele. Isso evita o "efeito cascata" visual onde os LEDs acendem um a um.

---

### Parte C — Função reutilizável

```python
# ============================================================
# Aula 01 — Parte C: funções acender_todos() e apagar_todos()
# ============================================================

from machine import Pin
import neopixel
import utime

PINO_DADOS = 4
# Pico: PINO_DADOS = 0

NUM_LEDS = 16

np = neopixel.NeoPixel(Pin(PINO_DADOS), NUM_LEDS)

def acender_todos(cor):
    """Acende todos os LEDs do anel com a mesma cor."""
    for i in range(NUM_LEDS):
        np[i] = cor
    np.write()

def apagar_todos():
    """Apaga todos os LEDs do anel."""
    acender_todos((0, 0, 0))    # cor apagado = (0, 0, 0)

# --- Demonstração ---
acender_todos((255, 0, 0))    # vermelho
utime.sleep(2)

acender_todos((0, 255, 0))    # verde
utime.sleep(2)

acender_todos((0, 0, 255))    # azul
utime.sleep(2)

apagar_todos()
```

> 💡 **Note:** `apagar_todos()` reutiliza `acender_todos()` passando `(0, 0, 0)` — sem repetição de código.

---

## 4. Circuito Wokwi — diagram.json

Cole o conteúdo abaixo no arquivo `diagram.json` do seu projeto Wokwi:

```json
{
  "version": 1,
  "author": "Uri Shaked",
  "editor": "wokwi",
  "parts": [
    {
      "type": "wokwi-esp32-devkit-v1",
      "id": "esp",
      "top": 0,
      "left": 0,
      "attrs": { "env": "micropython-20220117-v1.18" }
    },
    {
      "type": "wokwi-led-ring",
      "id": "ring1",
      "top": -25,
      "left": 125,
      "attrs": { "pixels": "16", "background": "black" }
    }
  ],
  "connections": [
    [ "esp:TX0", "$serialMonitor:RX", "", [] ],
    [ "esp:RX0", "$serialMonitor:TX", "", [] ],
    [ "ring1:DIN", "esp:D4", "green", [ "v6.44", "h-56.61", "v-14.4" ] ],
    [ "ring1:VCC", "esp:3V3", "red", [ "v0" ] ],
    [ "ring1:GND", "esp:GND.1", "black", [ "v0" ] ]
  ],
  "dependencies": {}
}
```

> ✅ Circuito validado — projeto disponível em [wokwi.com/projects/474715111472158721](https://wokwi.com/projects/474715111472158721)

---

## 5. Experimento

Execute a **Parte A** e responda:

**a)** O que acontece se você chamar `np[0] = (255, 0, 0)` mas **não** chamar `np.write()`?

> _________________________________________________________________

**b)** Qual linha você alteraria para acender o LED da posição 8 (lado oposto) em vez do LED 0? Escreva a linha correta:

```python
np[_____] = (_____, _____, _____)
```

**c)** Complete a tabela com as tuplas corretas:

| Cor desejada | Tupla (R, G, B) |
|---|---|
| Amarelo (vermelho + verde, sem azul) | `(_____, _____, _____)` |
| Roxo (vermelho + azul, sem verde) | `(_____, _____, _____)` |
| Branco em metade do brilho | `(_____, _____, _____)` |

---

## 6. Desafio

**Desafio principal:** acenda os LEDs de **índice par** (0, 2, 4, ..., 14) na cor **vermelha** e os de **índice ímpar** (1, 3, 5, ..., 15) na cor **azul**, simultaneamente.

```python
for i in range(NUM_LEDS):
    if i % 2 == _____:          # condição para par
        np[i] = (_____, _____, _____)   # vermelho
    else:
        np[i] = (_____, _____, _____)   # azul
np.write()
```

**Desafio bônus:** transforme o desafio principal em uma função `dois_tons(cor_par, cor_impar)` que recebe as duas cores como parâmetro e aplica o padrão ao anel:

```python
def dois_tons(cor_par, cor_impar):
    for i in range(NUM_LEDS):
        if _____:
            np[i] = _____
        else:
            np[i] = _____
    _____          # não esqueça!
```

---

## Resumo da aula

- O WS2812B é um LED RGB inteligente controlado por **um único fio de dados**
- LEDs são acessados por **índice**: `np[0]` a `np[15]`
- Cores são definidas por **tuplas** `(R, G, B)` com valores de 0 a 255
- `np[i] = cor` prepara a cor na memória — `np.write()` envia o sinal ao anel
- Para acender **todos**, use um `for` e chame `np.write()` **uma vez** ao final

---

*← [Início](../index.md) | Próxima → [Aula 2: Efeitos com Lista](./aula02-efeitos-lista.md)*
