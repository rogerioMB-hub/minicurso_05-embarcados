---
layout: default
title: "Aula 05-extra — Codificadores e Decodificadores"
---

# Aula 05-extra — Codificadores e Decodificadores

> **Duração estimada:** 20 minutos  
> **Bloco:** apoio opcional — Seção 2: Display de 7 Segmentos  
> **Quando ler:** antes da [Aula 6: Display de 7 Segmentos](./aula06-display-7-segmentos.md)

---

## Objetivos

Ao final desta aula você será capaz de:

- Diferenciar um **codificador** de um **decodificador**
- Representar os dígitos de 0 a 9 em **BCD** (4 bits)
- Ler a **tabela-verdade** de um decodificador BCD → 7 segmentos
- Reconhecer os CIs decodificadores CD4511 e 74LS47 e quando cada um é usado
- Entender por que, no ESP32 e no Pico, o decodificador vira **uma tabela dentro do programa**

> 💡 **Esta aula não tem circuito.** O código roda no Wokwi só com o ESP32 (ou o Pico) e mostra tudo no terminal.

---

## 1. Conceito

### O problema: pessoas leem dígitos, circuitos leem bits

Dentro de um circuito digital o número 7 é guardado como `0111`. Para uma pessoa, ele precisa aparecer como o desenho "7". No caminho inverso, quando alguém aperta a tecla "7" de um teclado, o circuito precisa transformar essa tecla em `0111`.

Os dois blocos que fazem essas traduções são o **codificador** e o **decodificador**.

![Codificador e decodificador — teclado de 10 teclas, código BCD e display de 7 segmentos](../assets/codificador_decodificador_blocos.svg)

| Bloco | Entradas | Saídas | Exemplo |
|---|---|---|---|
| **Codificador** | muitas (uma ativa por vez) | poucas (um código) | 10 teclas → 4 bits BCD |
| **Decodificador** | poucas (um código) | muitas | 4 bits BCD → 7 segmentos |

> **Regra para lembrar:** o **co**dificador **co**mprime (muitas linhas → um código); o **de**codificador **de**sdobra (um código → muitas linhas).

---

### BCD — decimal codificado em binário

**BCD** (*Binary-Coded Decimal*) representa cada dígito decimal, de 0 a 9, com 4 bits. É o binário que você já conhece, limitado a dez valores:

| Dígito | BCD (D C B A) |
|:---:|:---:|
| 0 | `0000` |
| 1 | `0001` |
| 2 | `0010` |
| 3 | `0011` |
| 4 | `0100` |
| 5 | `0101` |
| 6 | `0110` |
| 7 | `0111` |
| 8 | `1000` |
| 9 | `1001` |

As combinações de `1010` a `1111` (10 a 15) **não são usadas** em BCD.

> 📖 **Saiba mais:** a conversão entre binário e decimal e a leitura de bits individuais com `>>` e `&` foram trabalhadas no Mini-curso 01. Resumo: `(valor >> i) & 1` devolve o bit `i` de `valor`. → [Mini-curso 01 · Aula 3: Listas de pinos e máscara de bits](https://rogeriomb-hub.github.io/minicurso_01-embarcados/aulas/aula03-listas-mascaras)

Um número com mais de um dígito usa um grupo de 4 bits **por dígito**. O número 17, por exemplo, fica `0001 0111` em BCD: o grupo da dezena (1) e o grupo da unidade (7). Essa ideia de tratar cada dígito separadamente volta na Aula 7, quando cada display recebe o seu dígito.

---

### O display de 7 segmentos (visão rápida)

O display tem sete segmentos, chamados de **a** até **g**, mais o ponto decimal **dp**:

![Mapa dos segmentos a até g e dp, com o número do bit de cada um](../assets/7seg_mapa_segmentos.svg)

Para desenhar o "7" acendem os segmentos **a**, **b** e **c**. Para o "8", todos. Os detalhes elétricos do componente ficam para a Aula 6.

---

### Tabela-verdade do decodificador BCD → 7 segmentos

Esta é a tabela que um decodificador implementa. `1` significa segmento aceso (display de **catodo comum**, o que usamos em aula):

| Dígito | BCD | a | b | c | d | e | f | g |
|:---:|:---:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| 0 | `0000` | 1 | 1 | 1 | 1 | 1 | 1 | 0 |
| 1 | `0001` | 0 | 1 | 1 | 0 | 0 | 0 | 0 |
| 2 | `0010` | 1 | 1 | 0 | 1 | 1 | 0 | 1 |
| 3 | `0011` | 1 | 1 | 1 | 1 | 0 | 0 | 1 |
| 4 | `0100` | 0 | 1 | 1 | 0 | 0 | 1 | 1 |
| 5 | `0101` | 1 | 0 | 1 | 1 | 0 | 1 | 1 |
| 6 | `0110` | 1 | 0 | 1 | 1 | 1 | 1 | 1 |
| 7 | `0111` | 1 | 1 | 1 | 0 | 0 | 0 | 0 |
| 8 | `1000` | 1 | 1 | 1 | 1 | 1 | 1 | 1 |
| 9 | `1001` | 1 | 1 | 1 | 1 | 0 | 1 | 1 |

Confira duas linhas olhando a figura do display: no "1" só **b** e **c** acendem; no "0" todos acendem menos o **g** (o traço do meio).

---

### O decodificador em hardware

Antes dos microcontroladores, essa tabela era resolvida por um circuito integrado dedicado. Os dois mais comuns:

| CI | Display | Saída ativa em | Datasheet |
|---|---|---|---|
| **CD4511B** (CMOS) | catodo comum | nível **alto** | [TI CD4511B](https://www.ti.com/lit/ds/symlink/cd4511b.pdf) |
| **SN74LS47** (TTL) | anodo comum | nível **baixo** | [TI SN74LS47](https://www.ti.com/lit/ds/symlink/sn74ls47.pdf) |

Os dois recebem 4 bits BCD e entregam os 7 sinais dos segmentos. O CD4511 tem ainda entradas de teste de lâmpada (LT), apagamento (BL) e uma trava (*latch*) que guarda o último código recebido — guarde essa palavra *latch*: ela aparece de novo no 74HC595, na Aula 7.

---

### A virada: o decodificador vira uma tabela no programa

Com um ESP32 ou um Pico não precisamos do CI decodificador. O microcontrolador **guarda a tabela-verdade na memória** e liga os segmentos diretamente. A tabela acima vira, por exemplo, uma lista de listas:

```python
DIGITOS = [
    [1, 1, 1, 1, 1, 1, 0],   # 0
    [0, 1, 1, 0, 0, 0, 0],   # 1
    # ... uma linha por dígito
]
```

Isso traz duas vantagens:

- **Flexibilidade:** dá para desenhar letras (A, b, C, d, E, F) e símbolos (traço, grau) só acrescentando linhas na tabela.
- **Menos componentes:** um CI a menos na placa.

O custo é usar mais pinos do microcontrolador (um por segmento). A Aula 7 resolve esse custo com o 74HC595.

---

## 2. Circuito

Nenhum. Crie um projeto **ESP32 + MicroPython** no Wokwi (ou **Raspberry Pi Pico + MicroPython**) e use apenas o terminal.

---

## 3. Código

### Parte A — Codificador: tecla → BCD

O "teclado" é uma lista de 10 posições em que só uma vale `1` (a tecla apertada). O codificador procura essa posição e devolve o seu código BCD.

```python
# ============================================================
# Aula 05-extra — Parte A: codificador decimal → BCD
# Mini-curso 05 — Seção 2: Display de 7 Segmentos
# Roda igual no ESP32 e no Pico (só usa o terminal)
# ============================================================

def codificar(teclas):
    """Recebe uma lista de 10 valores (0 ou 1) com UMA tecla em 1.
    Devolve o número da tecla apertada, ou -1 se nenhuma estiver."""
    for numero, estado in enumerate(teclas):
        if estado == 1:
            return numero
    return -1

# tecla 7 apertada (posições 0 a 9)
teclas = [0, 0, 0, 0, 0, 0, 0, 1, 0, 0]

numero = codificar(teclas)
print("Teclas:", teclas)
print("Tecla apertada:", numero)
print("Código BCD:    {:04b}".format(numero))
```

> 💡 `"{:04b}".format(n)` escreve `n` em binário com 4 casas, completando com zeros à esquerda. É o mesmo recurso usado para imprimir máscaras no Mini-curso 01.

---

### Parte B — Decodificador: BCD → segmentos

Agora o caminho inverso: o programa percorre os dígitos de 0 a 9, mostra o código BCD e quais segmentos acenderiam.

```python
# ============================================================
# Aula 05-extra — Parte B: decodificador BCD → 7 segmentos
# ============================================================

NOMES = "abcdefg"

# Tabela-verdade: uma linha por dígito, colunas a b c d e f g
DIGITOS = [
    [1, 1, 1, 1, 1, 1, 0],   # 0
    [0, 1, 1, 0, 0, 0, 0],   # 1
    [1, 1, 0, 1, 1, 0, 1],   # 2
    [1, 1, 1, 1, 0, 0, 1],   # 3
    [0, 1, 1, 0, 0, 1, 1],   # 4
    [1, 0, 1, 1, 0, 1, 1],   # 5
    [1, 0, 1, 1, 1, 1, 1],   # 6
    [1, 1, 1, 0, 0, 0, 0],   # 7
    [1, 1, 1, 1, 1, 1, 1],   # 8
    [1, 1, 1, 1, 0, 1, 1],   # 9
]

def decodificar(n):
    """Devolve uma string com os nomes dos segmentos acesos no dígito n."""
    acesos = ""
    for i, estado in enumerate(DIGITOS[n]):
        if estado == 1:
            acesos = acesos + NOMES[i]
    return acesos

print("Dígito  BCD   Segmentos acesos")
for n in range(10):
    print("  {}    {:04b}  {}".format(n, n, decodificar(n)))
```

**Saída esperada:**

```
Dígito  BCD   Segmentos acesos
  0    0000  abcdef
  1    0001  bc
  2    0010  abdeg
  3    0011  abcdg
  4    0100  bcfg
  5    0101  acdfg
  6    0110  acdefg
  7    0111  abc
  8    1000  abcdefg
  9    1001  abcdfg
```

> 📖 **Saiba mais:** `enumerate()` devolve, a cada volta do `for`, a posição e o valor do item. Foi apresentado na Aula 3 do Mini-curso 01 e usado nas Aulas 2 e 3 deste mini-curso. → [Mini-curso 01 · Aula 3](https://rogeriomb-hub.github.io/minicurso_01-embarcados/aulas/aula03-listas-mascaras) · [Extra: for e range()](https://rogeriomb-hub.github.io/minicurso_01-embarcados/aulas/aula02-extra-for-range) · [Extra: funções](https://rogeriomb-hub.github.io/minicurso_01-embarcados/aulas/aula03-extra-funcoes)

---

## 4. Circuito Wokwi — diagram.json

Não há circuito nesta aula. Use o projeto padrão **ESP32 + MicroPython** do Wokwi, sem componentes extras.

---

## 5. Experimento

**a)** Na Parte A, mude a lista para a tecla 3 apertada. Qual código BCD aparece?

> _________________________________________________________________

**b)** Na Parte A, aperte duas teclas ao mesmo tempo (`teclas[2] = 1` e `teclas[5] = 1`). Qual número o codificador devolve? Por quê?

> _________________________________________________________________

**c)** Na Parte B, sem rodar o código, complete a linha do dígito 4 a partir da figura do display:

```python
[_____, _____, _____, _____, _____, _____, _____],   # 4
```

**d)** O número 20 em BCD tem dois grupos de 4 bits. Escreva-os:

> dezena: `____`   unidade: `____`

---

## 6. Desafio

**Desafio principal:** acrescente à tabela da Parte B as linhas para as letras **A**, **b**, **C**, **d**, **E** e **F** (posições 10 a 15) e mude o laço para `range(16)`. Desenhe cada letra no papel antes de escrever a linha.

```python
    [_____, _____, _____, _____, _____, _____, _____],   # A (10)
    [0, 0, 1, 1, 1, 1, 1],                               # b (11)
    # ...
```

**Desafio bônus:** crie a função `codificar_bcd(n)` que devolve uma **lista** com os 4 bits de `n`, do mais significativo para o menos significativo. Use `(n >> i) & 1`.

```python
def codificar_bcd(n):
    bits = []
    for i in range(3, -1, -1):
        bits.append(_____)
    return bits

print(codificar_bcd(9))   # → [1, 0, 0, 1]
```

---

## Resumo da aula

- **Codificador:** muitas entradas, poucas saídas — por exemplo, 10 teclas → 4 bits BCD
- **Decodificador:** poucas entradas, muitas saídas — por exemplo, 4 bits BCD → 7 segmentos
- **BCD** representa cada dígito decimal com 4 bits; números maiores usam um grupo por dígito
- CIs como **CD4511** (catodo comum) e **74LS47** (anodo comum) implementam a tabela-verdade em hardware
- No ESP32 e no Pico, a **tabela-verdade vira uma estrutura de dados** no programa — lista, dicionário ou tupla, como você verá na Aula 6

---

*← [Aula 5: Meteoro, Respiração e Cometa](./aula05-meteoro-respiracao-cometa.md) | Próxima → [Aula 6: Display de 7 Segmentos](./aula06-display-7-segmentos.md)*
