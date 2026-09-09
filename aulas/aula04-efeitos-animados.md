---
layout: default
title: "Aula 4 — Efeitos Animados"
---

# Aula 4 — Efeitos Animados

> **Duração estimada:** 30 minutos  
> **Bloco:** 4 de 5 — Seção NeoPixel

---

## Objetivos

Ao final desta aula você será capaz de:

- Controlar a velocidade de uma animação com `utime.sleep_ms()`
- Entender o conceito de "frame" em animações embarcadas
- Criar um spinner refinado com velocidade configurável
- Animar o arco-íris girando continuamente pelo anel
- Criar um efeito de pulso (fade in/out) usando divisão inteira `//`

---

## 1. Conceito

### Animação embarcada: frames e sleep_ms

Uma animação é uma sequência de imagens estáticas exibidas rapidamente — cada imagem é um **frame**. No NeoPixel, cada frame é um estado completo do anel: definimos todas as cores, chamamos `np.write()` e aguardamos um tempo antes do próximo frame.

```
frame 1 → np.write() → sleep_ms(80) → frame 2 → np.write() → sleep_ms(80) → ...
```

O tempo entre frames — controlado por `utime.sleep_ms()` — define a velocidade da animação:

| `sleep_ms` | Efeito visual |
|:---:|---|
| 200 ms | lento, perceptível quadro a quadro |
| 80 ms | fluido, agradável |
| 20 ms | muito rápido, quase contínuo |

Usar uma constante `VELOCIDADE_MS` facilita ajustar a animação inteira em um único lugar:

```python
VELOCIDADE_MS = 80      # altere aqui para mudar toda a animação

while True:
    # ... prepara o frame ...
    np.write()
    utime.sleep_ms(VELOCIDADE_MS)
```

### Por que np.write() fora do loop interno?

Dentro do loop de animação, há dois loops: o **externo** (frames) e o **interno** (atribuição de cores a cada LED). O `np.write()` deve ficar no loop **externo** — uma chamada por frame:

```python
while True:                        # loop externo — frames
    for i in range(NUM_LEDS):      # loop interno — atribuição
        np[i] = calcular_cor(i)
    np.write()                     # ← aqui: uma vez por frame
    utime.sleep_ms(VELOCIDADE_MS)
```

Se `np.write()` fosse chamado dentro do loop interno, o anel seria atualizado LED a LED — criando um efeito de "varredura" indesejado e desperdiçando tempo de processamento.

### Divisão inteira // para escalar brilho

Para criar o efeito de pulso, precisamos variar o brilho de 0 a 255 em passos discretos. O operador `//` (divisão inteira) faz esse cálculo sem gerar valores fracionários — o NeoPixel só aceita inteiros:

```python
# passo vai de 0 a 20
intensidade = (255 * passo) // 20    # resultado: 0, 12, 25, 38, ... 255
```

A divisão inteira descarta a parte decimal e garante que o resultado seja sempre um `int` válido para a tupla RGB.

> 🔗 **Relembre:** o operador `//` foi apresentado na [Aula 4 do Mini-curso 01 — Deslocamento e Escrita Direta em Porta](https://rogeriomb-hub.github.io/minicurso_01-embarcados/aulas/aula04-deslocamento-escrita-porta), no contexto de cálculo com registradores. O comportamento é o mesmo aqui.

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

### Parte A — Spinner refinado com velocidade configurável

```python
# ============================================================
# Aula 04 — Parte A: spinner com velocidade configurável
# Mini-curso 05 — NeoPixel WS2812B
# Plataforma principal: ESP32
# ============================================================

from machine import Pin
import neopixel
import utime

PINO_DADOS = 4
# Pico: PINO_DADOS = 0

NUM_LEDS   = 16
VELOCIDADE_MS = 80      # ajuste aqui para mudar a velocidade

np = neopixel.NeoPixel(Pin(PINO_DADOS), NUM_LEDS)

# --- Paleta do spinner: 4 cores que se repetem ---
cores = [
    (255,   0,   0),   # vermelho
    (  0, 255,   0),   # verde
    (  0,   0, 255),   # azul
    (255, 255,   0),   # amarelo
] * 4                  # repete para preencher 16 LEDs

# --- Loop de animação ---
while True:
    # frame: aplica as cores atuais
    for i, cor in enumerate(cores):
        np[i] = cor
    np.write()                          # uma chamada por frame

    utime.sleep_ms(VELOCIDADE_MS)       # pausa entre frames

    # rotaciona para o próximo frame
    cores = cores[1:] + cores[:1]
```

> 💡 **Experimente:** troque `VELOCIDADE_MS = 80` por `20` e depois por `200`. Observe como a mesma animação parece completamente diferente.

---

### Parte B — Arco-íris girante

```python
# ============================================================
# Aula 04 — Parte B: arco-íris girando continuamente
# ============================================================

from machine import Pin
import neopixel
import utime

PINO_DADOS = 4
# Pico: PINO_DADOS = 0

NUM_LEDS      = 16
VELOCIDADE_MS = 60

np = neopixel.NeoPixel(Pin(PINO_DADOS), NUM_LEDS)

# --- As 7 cores do arco-íris, expandidas para 16 LEDs ---
arco_iris = [
    (255,   0,   0),   # vermelho
    (255, 127,   0),   # laranja
    (255, 255,   0),   # amarelo
    (  0, 255,   0),   # verde
    (  0,   0, 255),   # azul
    ( 75,   0, 130),   # anil
    (148,   0, 211),   # violeta
]

# monta lista de 16 elementos repetindo o padrão de 7
cores = (arco_iris * 3)[:NUM_LEDS]    # repete e recorta em 16

# --- Loop de animação ---
while True:
    for i, cor in enumerate(cores):
        np[i] = cor
    np.write()

    utime.sleep_ms(VELOCIDADE_MS)

    cores = cores[1:] + cores[:1]      # rotaciona uma posição
```

> 💡 **Observe:** `(arco_iris * 3)[:NUM_LEDS]` repete a lista 3 vezes (21 elementos) e recorta os primeiros 16. É uma forma compacta de preencher qualquer tamanho de anel sem loop manual.

---

### Parte C — Pulso de brilho (fade in/out)

```python
# ============================================================
# Aula 04 — Parte C: pulso de brilho — fade in/out
# ============================================================

from machine import Pin
import neopixel
import utime

PINO_DADOS = 4
# Pico: PINO_DADOS = 0

NUM_LEDS  = 16
PASSOS    = 20          # quantidade de níveis de brilho
PAUSA_MS  = 30          # pausa entre cada nível

np = neopixel.NeoPixel(Pin(PINO_DADOS), NUM_LEDS)

# --- Cor base do pulso (apenas o canal vermelho varia) ---
COR_R = 255   # intensidade máxima do vermelho
COR_G = 0
COR_B = 80    # leve toque de azul — tom rosa-avermelhado

def aplicar_brilho(passo):
    """Acende todos os LEDs com brilho proporcional ao passo.
    passo 0 = apagado, passo PASSOS = brilho máximo.
    Usa // para garantir resultado inteiro (NeoPixel não aceita float).
    """
    r = (COR_R * passo) // PASSOS    # escala 0..255 em PASSOS níveis
    g = (COR_G * passo) // PASSOS
    b = (COR_B * passo) // PASSOS
    for i in range(NUM_LEDS):
        np[i] = (r, g, b)
    np.write()

# --- Loop de pulso: sobe e desce indefinidamente ---
while True:
    # fade in: brilho cresce de 0 até PASSOS
    for passo in range(0, PASSOS + 1):
        aplicar_brilho(passo)
        utime.sleep_ms(PAUSA_MS)

    # fade out: brilho cai de PASSOS até 0
    for passo in range(PASSOS, -1, -1):
        aplicar_brilho(passo)
        utime.sleep_ms(PAUSA_MS)
```

> 💡 **Por que `//` e não `/`?** A tupla RGB só aceita inteiros. `(255 * 3) / 20` retorna `38.25` — isso causaria um erro. `(255 * 3) // 20` retorna `38` — inteiro válido.

---

## 4. Circuito Wokwi — diagram.json

Mesmo `diagram.json` da Aula 1 — nenhuma alteração necessária.

> ✅ Circuito validado — projeto disponível em [wokwi.com/projects/474715111472158721](https://wokwi.com/projects/474715111472158721)

---

## 5. Experimento

Execute a **Parte C** (pulso) e responda:

**a)** O que acontece se você aumentar `PASSOS` de `20` para `50`? E se diminuir para `5`? O que essa constante controla de fato?

> _________________________________________________________________  
> _________________________________________________________________

**b)** Por que o resultado de `(255 * passo) // PASSOS` é sempre um inteiro? O que aconteceria se usasse `/` em vez de `//`?

> _________________________________________________________________  
> _________________________________________________________________

**c)** O loop `range(PASSOS, -1, -1)` percorre os passos em ordem decrescente. Complete a tabela para `PASSOS = 4`:

| Iteração | `passo` | `(255 * passo) // 4` |
|:---:|:---:|:---:|
| 1ª | `_____` | `_____` |
| 2ª | `_____` | `_____` |
| 3ª | `_____` | `_____` |
| 4ª | `_____` | `_____` |
| 5ª | `_____` | `_____` |

**d)** Na Parte A, mova `np.write()` para dentro do `for i, cor in enumerate(cores)`. Execute e descreva o que muda visualmente:

> _________________________________________________________________

---

## 6. Desafio

**Desafio principal:** modifique o spinner (Parte A) para que a velocidade **aumente gradualmente** — começa lento e acelera até um limite, depois reinicia:

```python
VELOCIDADE_MIN = 20     # mais rápido
VELOCIDADE_MAX = 200    # mais lento
PASSO_MS       = 10     # quanto diminui por frame

velocidade = VELOCIDADE_MAX

while True:
    for i, cor in enumerate(cores):
        np[i] = cor
    np.write()
    utime.sleep_ms(velocidade)
    cores = cores[1:] + cores[:1]

    velocidade -= _____                     # acelera
    if velocidade < _____:                  # chegou no limite?
        velocidade = _____                  # reinicia
```

**Desafio bônus:** alterne automaticamente entre dois efeitos — arco-íris girante por 5 segundos e pulso por 5 segundos — repetindo indefinidamente:

```python
import utime

DURACAO_MS = 5000       # 5 segundos por efeito

while True:
    # --- fase arco-íris ---
    inicio = utime.ticks_ms()
    cores  = (arco_iris * 3)[:NUM_LEDS]

    while utime.ticks_diff(utime.ticks_ms(), inicio) < _____:
        for i, cor in enumerate(cores):
            np[i] = cor
        np.write()
        utime.sleep_ms(60)
        cores = cores[1:] + cores[:1]

    # --- fase pulso ---
    inicio = utime.ticks_ms()

    while utime.ticks_diff(utime.ticks_ms(), inicio) < _____:
        for passo in range(0, PASSOS + 1):
            aplicar_brilho(passo)
            utime.sleep_ms(PAUSA_MS)
        for passo in range(PASSOS, -1, -1):
            aplicar_brilho(passo)
            utime.sleep_ms(_____)
```

---

## Resumo da aula

- Cada `np.write()` é um **frame** — a animação é a sequência de frames no tempo
- `utime.sleep_ms()` controla a velocidade; uma constante `VELOCIDADE_MS` centraliza o ajuste
- `np.write()` deve ser chamado **uma vez por frame**, fora do loop de atribuição
- O operador `//` garante resultados inteiros ao escalar brilho — indispensável para tuplas RGB
- Rotação por fatiamento `lista[1:] + lista[:1]` é a base de qualquer efeito de movimento no anel

---

*← [Aula 3: Paleta com Dicionário](./aula03-paleta-dicionario.md) | Próxima → [Aula 5: Meteoro, Respiração e Cometa](./aula05-meteoro-respiracao-cometa.md)*
