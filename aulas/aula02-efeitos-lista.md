---
layout: default
title: "Aula 2 — Efeitos com Lista de Cores"
---

# Aula 2 — Efeitos com Lista de Cores

> **Duração estimada:** 30 minutos  
> **Bloco:** 2 de 5 — Seção NeoPixel

---

## Objetivos

Ao final desta aula você será capaz de:

- Armazenar cores em uma lista de tuplas RGB
- Percorrer a lista com `for` e `enumerate()` para acender LEDs por índice
- Criar um efeito de rotação deslocando cores pelo anel
- Montar um arco-íris fixo com 7 cores distribuídas pelos 16 LEDs

> 💡 **Novo aqui?** Esta aula usa tuplas `(R, G, B)` o tempo todo. Se esse conceito ainda não é familiar, leia antes a [Aula 00-extra: Tuplas em Python](./aula00-extra-tuplas.md).

---

## 1. Conceito

### Lista de tuplas como paleta de cores

Na Aula 1 definimos a cor de cada LED diretamente no código: `np[0] = (255, 0, 0)`. Isso funciona para um LED, mas se quisermos controlar 16 LEDs com cores diferentes, repetir esse padrão 16 vezes é impraticável.

A solução é armazenar as cores em uma **lista de tuplas**:

```python
paleta = [
    (255,   0,   0),   # vermelho
    (255, 127,   0),   # laranja
    (255, 255,   0),   # amarelo
]
```

Agora podemos acessar qualquer cor por índice — `paleta[0]` é vermelho, `paleta[1]` é laranja — e percorrer a lista com `for`.

### enumerate() — índice e valor juntos

Quando precisamos do **índice** e do **valor** ao mesmo tempo, usamos `enumerate()`:

```python
for i, cor in enumerate(paleta):
    np[i] = cor
```

Isso equivale a:

| Iteração | `i` | `cor` |
|:---:|:---:|---|
| 1ª | `0` | `(255, 0, 0)` |
| 2ª | `1` | `(255, 127, 0)` |
| 3ª | `2` | `(255, 255, 0)` |

### Rotação de lista por fatiamento

Para criar o efeito de "girar" as cores pelo anel, deslocamos a lista uma posição a cada ciclo:

```python
cores = cores[1:] + cores[:1]
```

Isso move o primeiro elemento para o final — todas as cores avançam uma posição, criando o efeito de movimento.

```
Antes:  [A, B, C, D]
Depois: [B, C, D, A]
```

---

## 2. Circuito

Mesmo circuito da Aula 1 — nenhuma alteração necessária.

| ESP32 | Anel NeoPixel |
|---|---|
| GPIO 4 | DIN |
| 3.3 V | VCC |
| GND | GND |

```
# Pico: use GPIO 0 no lugar de GPIO 4
```

*Link do Wokwi → [Minicurso 05 — Anel NeoPixel 16 LEDs](https://wokwi.com/projects/474715111472158721)*

---

## 3. Código

### Parte A — Paleta fixa: cada LED com sua cor

```python
# ============================================================
# Aula 02 — Parte A: lista de cores, um LED por cor
# Mini-curso 05 — NeoPixel WS2812B
# Plataforma principal: ESP32
# ============================================================

from machine import Pin
import neopixel

PINO_DADOS = 4
# Pico: PINO_DADOS = 0

NUM_LEDS = 16

np = neopixel.NeoPixel(Pin(PINO_DADOS), NUM_LEDS)

# --- Paleta: 16 cores para os 16 LEDs ---
paleta = [
    (255,   0,   0),   # 0  vermelho
    (255,  64,   0),   # 1  laranja-avermelhado
    (255, 127,   0),   # 2  laranja
    (255, 191,   0),   # 3  amarelo-alaranjado
    (255, 255,   0),   # 4  amarelo
    (128, 255,   0),   # 5  verde-amarelado
    (  0, 255,   0),   # 6  verde
    (  0, 255, 128),   # 7  verde-ciano
    (  0, 255, 255),   # 8  ciano
    (  0, 128, 255),   # 9  azul-ciano
    (  0,   0, 255),   # 10 azul
    ( 64,   0, 255),   # 11 azul-violeta
    (128,   0, 255),   # 12 violeta
    (191,   0, 255),   # 13 roxo
    (255,   0, 191),   # 14 rosa
    (255,   0, 128),   # 15 rosa-avermelhado
]

# --- Aplica uma cor por LED ---
for i, cor in enumerate(paleta):   # i = índice, cor = tupla
    np[i] = cor

np.write()
```

> 💡 **Observe:** a lista tem exatamente 16 elementos — um para cada LED. `enumerate()` entrega o índice `i` e a tupla `cor` juntos, sem precisar de um contador separado.

---

### Parte B — Efeito spinner (rotação)

```python
# ============================================================
# Aula 02 — Parte B: efeito spinner — cores giram pelo anel
# ============================================================

from machine import Pin
import neopixel
import utime

PINO_DADOS = 4
# Pico: PINO_DADOS = 0

NUM_LEDS = 16

np = neopixel.NeoPixel(Pin(PINO_DADOS), NUM_LEDS)

# --- Paleta inicial: apenas 4 cores que se repetem ---
cores = [
    (255,   0,   0),   # vermelho
    (  0, 255,   0),   # verde
    (  0,   0, 255),   # azul
    (255, 255,   0),   # amarelo
] * 4                  # repete 4x para preencher 16 LEDs

# --- Loop de rotação ---
while True:
    # aplica as cores atuais ao anel
    for i, cor in enumerate(cores):
        np[i] = cor
    np.write()

    utime.sleep_ms(80)             # velocidade da animação

    # rotaciona: move o primeiro elemento para o final
    cores = cores[1:] + cores[:1]
```

> 💡 **Experimente:** aumente ou diminua o valor de `sleep_ms` e observe a velocidade mudar. Valores menores = mais rápido.

---

### Parte C — Arco-íris fixo

```python
# ============================================================
# Aula 02 — Parte C: arco-íris com 7 cores distribuídas
# ============================================================

from machine import Pin
import neopixel

PINO_DADOS = 4
# Pico: PINO_DADOS = 0

NUM_LEDS = 16

np = neopixel.NeoPixel(Pin(PINO_DADOS), NUM_LEDS)

# --- As 7 cores do arco-íris ---
arco_iris = [
    (255,   0,   0),   # vermelho
    (255, 127,   0),   # laranja
    (255, 255,   0),   # amarelo
    (  0, 255,   0),   # verde
    (  0,   0, 255),   # azul
    ( 75,   0, 130),   # anil
    (148,   0, 211),   # violeta
]

# --- Distribui as 7 cores pelos 16 LEDs ---
# cada LED recebe a cor do índice (i % 7), repetindo o ciclo
for i in range(NUM_LEDS):
    np[i] = arco_iris[i % 7]   # % 7 garante que o índice nunca passa de 6

np.write()

# =============================================================
# PARA IR ALÉM — arco-íris por cálculo HSV → RGB (comentado)
#
# Em vez de uma lista fixa, podemos calcular as cores
# distribuindo matizes (hue) uniformemente no círculo cromático.
# Isso produz um arco-íris perfeito com qualquer número de LEDs.
#
# import math
#
# def hsv_para_rgb(h, s, v):
#     """Converte HSV (0-1) para tupla RGB (0-255).
#        h = matiz (hue): 0=vermelho, 0.33=verde, 0.66=azul, 1=vermelho
#        s = saturação: 0=cinza, 1=cor pura
#        v = valor (brilho): 0=preto, 1=brilho máximo
#     """
#     if s == 0:
#         r = g = b = int(v * 255)
#         return (r, g, b)
#     i = int(h * 6)
#     f = (h * 6) - i
#     p = v * (1 - s)
#     q = v * (1 - f * s)
#     t = v * (1 - (1 - f) * s)
#     i = i % 6
#     if i == 0: r, g, b = v, t, p
#     elif i == 1: r, g, b = q, v, p
#     elif i == 2: r, g, b = p, v, t
#     elif i == 3: r, g, b = p, q, v
#     elif i == 4: r, g, b = t, p, v
#     elif i == 5: r, g, b = v, p, q
#     return (int(r * 255), int(g * 255), int(b * 255))
#
# for i in range(NUM_LEDS):
#     hue = i / NUM_LEDS        # distribui matizes de 0 a 1
#     np[i] = hsv_para_rgb(hue, 1.0, 0.4)   # saturação=1, brilho=0.4
# np.write()
# =============================================================
```

> 💡 **Por que `i % 7`?** O operador `%` (resto da divisão) faz o índice "reiniciar" ao chegar em 7. Com 16 LEDs e 7 cores, o padrão se repete duas vezes com 2 LEDs extras — experimente visualizar no anel.

---

## 4. Circuito Wokwi — diagram.json

Mesmo `diagram.json` da Aula 1 — nenhuma alteração necessária.

> ✅ Circuito validado — projeto disponível em [wokwi.com/projects/474715111472158721](https://wokwi.com/projects/474715111472158721)

---

## 5. Experimento

Execute a **Parte B** (spinner) e responda:

**a)** A linha `cores = cores[1:] + cores[:1]` desloca as cores para a esquerda. Como você modificaria essa linha para girar na **direção oposta** (direita)?

```python
cores = cores[_____:] + cores[:_____]
```

**b)** Na Parte C, o que acontece se você substituir `i % 7` por `i % 3`? Quantas cores aparecem no anel? Por quê?

> _________________________________________________________________  
> _________________________________________________________________

**c)** O `enumerate(paleta)` na Parte A retorna pares `(índice, valor)`. Complete a tabela para as duas primeiras iterações:

| Iteração | `i` | `cor` |
|:---:|:---:|---|
| 1ª | `_____` | `_____` |
| 2ª | `_____` | `_____` |

---

## 6. Desafio

**Desafio principal:** crie um efeito de **pisca alternado** — os LEDs de índice par acendem em vermelho enquanto os ímpares ficam apagados; depois os ímpares acendem em azul enquanto os pares ficam apagados. O ciclo se repete indefinidamente.

```python
while True:
    # fase 1: pares = vermelho, ímpares = apagado
    for i in range(NUM_LEDS):
        if i % 2 == _____:
            np[i] = (_____, _____, _____)
        else:
            np[i] = (_____, _____, _____)
    np.write()
    utime.sleep_ms(_____)

    # fase 2: ímpares = azul, pares = apagado
    for i in range(NUM_LEDS):
        if _____:
            np[i] = (_____, _____, _____)
        else:
            np[i] = (_____, _____, _____)
    np.write()
    utime.sleep_ms(_____)
```

**Desafio bônus:** combine o arco-íris (Parte C) com a rotação (Parte B): monte a lista `arco_iris` com as 7 cores e faça-a girar pelo anel continuamente.

```python
cores = arco_iris * 3       # repete para cobrir 16 LEDs — por que * 3?
cores = cores[:NUM_LEDS]    # recorta exatamente 16 elementos

while True:
    for i, cor in enumerate(cores):
        np[i] = cor
    np.write()
    utime.sleep_ms(80)
    cores = _____             # rotaciona — complete aqui
```

---

## Resumo da aula

- Uma **lista de tuplas** é a forma natural de representar uma sequência de cores no NeoPixel
- `enumerate(lista)` entrega índice e valor juntos — evita contador manual
- O operador `%` (resto) repete um padrão ciclicamente por qualquer número de LEDs
- Rotacionar com `lista[1:] + lista[:1]` desloca as cores uma posição — base de efeitos de movimento
- Chame `np.write()` **uma vez** após preparar todos os LEDs, nunca dentro do loop de atribuição

---

*← [Aula 1: Primeiro Pixel](./aula01-primeiro-pixel.md) | Próxima → [Aula 3: Paleta com Dicionário](./aula03-paleta-dicionario.md)*
