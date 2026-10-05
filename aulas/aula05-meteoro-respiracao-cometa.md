---
layout: default
title: "Aula 5 — Meteoro, Respiração e Cometa"
---

# Aula 5 — Meteoro, Respiração e Cometa

> **Duração estimada:** 30 minutos  
> **Bloco:** 5 de 5 — Seção NeoPixel

---

## Objetivos

Ao final desta aula você será capaz de:

- Criar um rastro de atenuação progressiva usando divisão inteira `//`
- Implementar o efeito meteoro com cabeça brilhante e rastro que apaga
- Criar uma respiração suave com lista de intensidades pré-calculadas
- Implementar o efeito cometa com atenuação quadro a quadro
- Combinar índice circular com `%` para movimentar LEDs pelo anel

> 💡 **Atenção:** esta aula usa `range()` com contagem regressiva — ex: `range(PASSOS, -1, -1)`. Se esse uso não é familiar, leia antes a [Aula 2-extra: for e range() em MicroPython](https://rogeriomb-hub.github.io/minicurso_01-embarcados/aulas/aula02-extra-for-range) do Mini-curso 01.

---

## 1. Conceito

### Rastro por atenuação

Para simular um rastro que "some", reduzimos progressivamente o brilho dos LEDs que o LED principal já passou. A técnica é multiplicar cada canal da cor pelo fator de atenuação e usar `//` para manter o resultado inteiro:

```python
def atenuar(cor, fator):
    """Reduz o brilho de uma cor pelo fator dado (0.0 a 1.0).
    Usa // para garantir resultado inteiro — NeoPixel não aceita float.
    """
    r, g, b = cor
    return (r * fator // 100, g * fator // 100, b * fator // 100)
```

Chamando com `fator = 50` (50%), uma cor `(200, 0, 0)` vira `(100, 0, 0)` — metade do brilho. Aplicando repetidamente: `200 → 100 → 50 → 25 → 12 → 6 → 3 → 1 → 0` — o rastro some naturalmente.

### Respiração suave vs. pulso linear

Na Aula 4 criamos um pulso **linear**: o brilho sobe e desce em passos iguais. O resultado é correto, mas visualmente mecânico.

Uma **respiração** imita o ritmo de respirar: sobe devagar, atinge o pico, desce devagar — com uma curva suave. Isso pode ser calculado com `math.sin()`, mas a forma mais simples é usar uma **lista de intensidades pré-calculadas**:

```python
RESPIRACAO = [
    0, 2, 7, 15, 26, 39, 53, 67, 80, 91,
    98, 100, 98, 91, 80, 67, 53, 39, 26, 15,
    7, 2, 0
]
```

Cada valor é uma porcentagem (0–100) do brilho máximo. A distribuição não é uniforme — os valores próximos de 0 e 100 ficam mais juntos, criando a sensação de desaceleração nas extremidades.

> 💡 **Para ir além:** esses valores foram gerados aproximando `(1 - cos(x)) / 2` em 22 pontos igualmente espaçados entre 0 e 2π — a curva cosseno que imita a respiração natural. Em MicroPython, isso seria `import math` seguido de `math.cos(x)`. Se quiser experimentar, o princípio é o mesmo — a lista apenas evita o cálculo em tempo real.

### Cometa: cabeça e cauda

O efeito cometa combina dois mecanismos:

- **Cabeça** — um LED em brilho máximo percorre o anel usando `posicao % NUM_LEDS`, garantindo que o índice "reinicie" ao chegar no final
- **Cauda** — a cada frame, todos os LEDs já existentes têm seu brilho reduzido pelo fator de atenuação, antes de a nova cabeça ser posicionada

```
frame N:    [0, 0, 0, ..., 255, 180, 90, 30]   ← cabeça na pos 13
frame N+1:  [0, 0, 0, ...,   0, 127, 45, 15, 255]  ← cabeça avança
```

O resultado é uma cauda que some organicamente sem precisar controlar cada LED individualmente.

---

## 2. Circuito

Mesmo circuito da Aula 1 — nenhuma alteração necessária.

| ESP32 | Anel NeoPixel |
|---|---|
| GPIO 4 | DIN |
| 5 V | VCC (hardware real; Wokwi usa 3V3) |
| GND | GND |

```
# Pico: use GPIO 0 no lugar de GPIO 4
```

*Link do Wokwi → [Minicurso 05 — Anel NeoPixel 16 LEDs](https://wokwi.com/projects/474715111472158721)*

---

## 3. Código

### Parte A — Efeito meteoro

```python
# ============================================================
# Aula 05 — Parte A: efeito meteoro
# Mini-curso 05 — NeoPixel WS2812B
# Plataforma principal: ESP32
# ============================================================

from machine import Pin
import neopixel
import utime

PINO_DADOS = 4
# Pico: PINO_DADOS = 0

NUM_LEDS      = 16
VELOCIDADE_MS = 60
ATENUACAO     = 55      # % de brilho mantido a cada frame (0-100)
                        # 55% → rastro some em ~6 frames

np = neopixel.NeoPixel(Pin(PINO_DADOS), NUM_LEDS)

# --- Cor da cabeça do meteoro ---
COR_CABECA = (220, 180, 255)    # branco-azulado

def atenuar_anel():
    """Reduz o brilho de todos os LEDs pelo fator ATENUACAO.
    Chamada a cada frame antes de posicionar a nova cabeça.
    """
    for i in range(NUM_LEDS):
        r, g, b = np[i]
        np[i] = (
            r * ATENUACAO // 100,
            g * ATENUACAO // 100,
            b * ATENUACAO // 100,
        )

# --- Loop contínuo do meteoro ---
posicao = 0

while True:
    atenuar_anel()                      # rastro some um pouco
    np[posicao % NUM_LEDS] = COR_CABECA # cabeça na posição atual
    np.write()

    utime.sleep_ms(VELOCIDADE_MS)
    posicao += 1                        # avança uma posição

# --- Para fazer apenas UMA passagem pelo anel, substitua o while por: ---
# for posicao in range(NUM_LEDS + 8):   # +8 para o rastro sumir completamente
#     atenuar_anel()
#     if posicao < NUM_LEDS:
#         np[posicao] = COR_CABECA
#     np.write()
#     utime.sleep_ms(VELOCIDADE_MS)
```

> 💡 **Observe:** `posicao % NUM_LEDS` faz o índice "reiniciar" automaticamente ao chegar em 16 — o meteoro circula sem `if`. O mesmo operador `%` usado na Aula 2 para o arco-íris.

---

### Parte B — Respiração suave

```python
# ============================================================
# Aula 05 — Parte B: respiração suave com lista de intensidades
# ============================================================

from machine import Pin
import neopixel
import utime

PINO_DADOS = 4
# Pico: PINO_DADOS = 0

NUM_LEDS = 16
PAUSA_MS = 40           # pausa entre cada intensidade da lista

np = neopixel.NeoPixel(Pin(PINO_DADOS), NUM_LEDS)

# --- Lista de intensidades (% de 0 a 100) ---
# Distribui os valores de forma não-linear: sobe e desce com
# aceleração suave nas extremidades, imitando o ritmo da respiração.
# Para calcular com math.sin(): import math e use
#   round((1 - math.cos(math.pi * i / 11)) * 50) para i em range(23)
RESPIRACAO = [
     0,  2,  7, 15, 26, 39, 53, 67, 80, 91,
    98, 100, 98, 91, 80, 67, 53, 39, 26, 15,
     7,  2,  0,
]

# --- Cor base da respiração ---
COR_BASE = (0, 180, 255)    # azul-ciano

def aplicar_respiracao(intensidade_pct):
    """Acende todos os LEDs com a cor base em intensidade_pct % do brilho."""
    r = COR_BASE[0] * intensidade_pct // 100
    g = COR_BASE[1] * intensidade_pct // 100
    b = COR_BASE[2] * intensidade_pct // 100
    for i in range(NUM_LEDS):
        np[i] = (r, g, b)
    np.write()

# --- Loop de respiração ---
while True:
    for intensidade in RESPIRACAO:
        aplicar_respiracao(intensidade)
        utime.sleep_ms(PAUSA_MS)
```

> 💡 **Compare com a Aula 4:** o pulso linear usava `range(0, PASSOS+1)` — passos iguais. Aqui a lista `RESPIRACAO` concentra mais valores perto de 0 e 100, criando a desaceleração natural nas extremidades. O código é quase idêntico — só a lista muda.

---

### Parte C — Efeito cometa

```python
# ============================================================
# Aula 05 — Parte C: efeito cometa
# ============================================================

from machine import Pin
import neopixel
import utime

PINO_DADOS = 4
# Pico: PINO_DADOS = 0

NUM_LEDS      = 16
VELOCIDADE_MS = 50
ATENUACAO     = 70      # % mantido por frame — 70% cria cauda mais longa
                        # que o meteoro (55%); experimente valores entre 40-85

np = neopixel.NeoPixel(Pin(PINO_DADOS), NUM_LEDS)

# --- Cores do cometa ---
COR_CABECA = (255, 255, 255)    # cabeça branca
COR_BASE   = (255,  80,   0)    # laranja — cor da cauda no pico

def atenuar_anel():
    """Atenua todos os LEDs em direção à cor base da cauda.
    Em vez de ir para preto puro, mistura levemente com COR_BASE,
    dando à cauda uma coloração quente antes de apagar.
    """
    for i in range(NUM_LEDS):
        r, g, b = np[i]
        # atenua em direção ao preto
        nr = r * ATENUACAO // 100
        ng = g * ATENUACAO // 100
        nb = b * ATENUACAO // 100
        # adiciona um toque da cor base para colorir a cauda
        nr = min(255, nr + COR_BASE[0] * (100 - ATENUACAO) // 200)
        ng = min(255, ng + COR_BASE[1] * (100 - ATENUACAO) // 200)
        nb = min(255, nb + COR_BASE[2] * (100 - ATENUACAO) // 200)
        np[i] = (nr, ng, nb)

# --- Loop contínuo do cometa ---
posicao = 0

while True:
    atenuar_anel()
    np[posicao % NUM_LEDS] = COR_CABECA
    np.write()

    utime.sleep_ms(VELOCIDADE_MS)
    posicao += 1

# --- Para fazer apenas UMA volta, substitua o while por: ---
# for posicao in range(NUM_LEDS + 12):  # +12 para a cauda sumir
#     atenuar_anel()
#     if posicao < NUM_LEDS:
#         np[posicao % NUM_LEDS] = COR_CABECA
#     np.write()
#     utime.sleep_ms(VELOCIDADE_MS)
```

> 💡 **Diferença entre meteoro e cometa:** o meteoro usa uma cor única para a cabeça e vai direto para preto no rastro. O cometa mistura a atenuação com `COR_BASE`, dando à cauda uma coloração quente antes de apagar — visualmente mais rico.

---

## 4. Circuito Wokwi — diagram.json

Mesmo `diagram.json` da Aula 1 — nenhuma alteração necessária.

> ✅ Circuito validado — projeto disponível em [wokwi.com/projects/474715111472158721](https://wokwi.com/projects/474715111472158721)

---

## 5. Experimento

Execute a **Parte A** (meteoro) e responda:

**a)** Altere `ATENUACAO` de `55` para `80`. O rastro fica mais longo ou mais curto? Por quê?

> _________________________________________________________________  
> _________________________________________________________________

**b)** Na Parte B, o que acontece se você duplicar todos os valores de `RESPIRACAO` pela metade — ou seja, usar `intensidade // 2` dentro de `aplicar_respiracao()`? O que muda visualmente?

> _________________________________________________________________

**c)** Na Parte C, o que acontece se `ATENUACAO = 0`? E se `ATENUACAO = 100`?

| `ATENUACAO` | Comportamento esperado |
|:---:|---|
| `0` | _____________________________ |
| `100` | _____________________________ |

**d)** O `posicao % NUM_LEDS` aparece nas Partes A e C. Explique com suas palavras por que ele é necessário:

> _________________________________________________________________  
> _________________________________________________________________

---

## 6. Desafio

**Desafio principal:** crie um **meteoro duplo** — dois meteoros em lados opostos do anel, separados por 8 posições, girando simultaneamente:

```python
posicao = 0

while True:
    atenuar_anel()

    cabeca1 = posicao % NUM_LEDS
    cabeca2 = (posicao + _____) % NUM_LEDS   # lado oposto

    np[cabeca1] = COR_CABECA
    np[cabeca2] = (_____,  _____, _____)      # segunda cor — escolha você

    np.write()
    utime.sleep_ms(VELOCIDADE_MS)
    posicao += _____
```

**Desafio bônus:** crie um cometa cuja cabeça muda de cor gradualmente a cada volta — começa vermelho, passa por laranja, amarelo e volta ao vermelho. Use uma lista de cores para a cabeça e avance para a próxima a cada `NUM_LEDS` frames:

```python
CORES_CABECA = [
    (255,   0,   0),   # vermelho
    (255, 127,   0),   # laranja
    (255, 255,   0),   # amarelo
    (  0, 255,   0),   # verde
]

posicao    = 0
idx_cor    = 0

while True:
    atenuar_anel()
    np[posicao % NUM_LEDS] = CORES_CABECA[_____]
    np.write()
    utime.sleep_ms(VELOCIDADE_MS)

    posicao += 1

    # a cada volta completa, avança para a próxima cor da cabeça
    if posicao % _____ == 0:
        idx_cor = (idx_cor + 1) % _____
```

---

## Resumo da aula

- **Rastro** — atenuar todos os LEDs a cada frame com `r * fator // 100` produz o efeito de "some naturalmente"
- **`% NUM_LEDS`** — mantém o índice sempre dentro do anel, eliminando a necessidade de `if` para reiniciar
- **Respiração** — uma lista de intensidades não-lineares cria suavidade nas extremidades; o código é quase igual ao pulso da Aula 4
- **Cometa** — combina atenuação com mistura de cor base, dando coloração à cauda antes de apagar
- **Uma passagem** — trocar `while True` por `for posicao in range(NUM_LEDS + N)` executa o efeito uma única vez; `N` extra garante que o rastro suma completamente

---

*← [Aula 4: Efeitos Animados](./aula04-efeitos-animados.md) | Próxima → [Seção 2 · Aula 05-extra: Codificadores e Decodificadores](./aula05-extra-codificadores-decodificadores.md)*
