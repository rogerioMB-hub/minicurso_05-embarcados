---
layout: default
title: "Aula 3 — Paleta de Cores com Dicionário"
---

# Aula 3 — Paleta de Cores com Dicionário

> **Duração estimada:** 30 minutos  
> **Bloco:** 3 de 5 — Seção NeoPixel

---

## Objetivos

Ao final desta aula você será capaz de:

- Entender o que é um dicionário em Python e como ele difere de uma lista
- Criar e consultar um dicionário com chaves string e valores tupla
- Usar `.get()` para acessar chaves com segurança
- Montar uma paleta de cores nomeadas para o anel NeoPixel
- Acender LEDs por nome de cor em vez de por tupla direta

> 💡 **Novo aqui?** Esta aula usa tuplas `(R, G, B)` como valores do dicionário. Se esse conceito ainda não é familiar, leia antes a [Aula 00-extra: Tuplas em Python](./aula00-extra-tuplas.md).

---

## 1. Conceito

### O que é um dicionário?

Imagine uma **agenda telefônica**: você não procura um número pela posição ("o terceiro da lista") — você procura pelo **nome**. A agenda associa cada nome a um número.

Um **dicionário Python** funciona da mesma forma: associa uma **chave** a um **valor**.

```python
agenda = {
    "Ana":    "51 99100-0001",
    "Bruno":  "51 99100-0002",
    "Carlos": "51 99100-0003",
}
```

Para consultar, use a chave entre colchetes:

```python
print(agenda["Ana"])      # → "51 99100-0001"
print(agenda["Bruno"])    # → "51 99100-0002"
```

Comparando com uma lista:

| | Lista | Dicionário |
|---|---|---|
| Acesso por | Índice numérico `[0]`, `[1]`... | Chave string `["Ana"]` |
| Ordem importa? | Sim | Não necessariamente |
| Uso típico | Sequência de itens | Associação nome → valor |

### Acessando com segurança: .get()

Se você tentar acessar uma chave que não existe, Python levanta um erro:

```python
print(agenda["Zeca"])     # KeyError: 'Zeca'
```

Para evitar o erro, use `.get()` com um valor padrão:

```python
print(agenda.get("Zeca", "não encontrado"))   # → "não encontrado"
```

Se a chave existir, retorna o valor normalmente. Se não existir, retorna o valor padrão — sem erro.

### Listando as chaves disponíveis

```python
print(agenda.keys())
# → dict_keys(['Ana', 'Bruno', 'Carlos'])
```

Útil para mostrar ao usuário quais opções estão disponíveis.

---

### Dicionário como paleta de cores

Agora aplique a mesma ideia às cores do NeoPixel. Em vez de lembrar que vermelho é `(255, 0, 0)`, criamos uma paleta nomeada:

```python
CORES = {
    "vermelho": (255,   0,   0),
    "verde":    (  0, 255,   0),
    "azul":     (  0,   0, 255),
    "amarelo":  (255, 255,   0),
    "ciano":    (  0, 255, 255),
    "magenta":  (255,   0, 255),
    "branco":   (255, 255, 255),
    "apagado":  (  0,   0,   0),
}
```

Agora o código fica legível como uma frase:

```python
np[0] = CORES["vermelho"]    # muito mais claro que np[0] = (255, 0, 0)
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

*Link do Wokwi → mesmo projeto das aulas anteriores*

---

## 3. Código

### Parte A — Paleta nomeada: acender LED por nome

```python
# ============================================================
# Aula 03 — Parte A: paleta de cores com dicionário
# Mini-curso 05 — NeoPixel WS2812B
# Plataforma principal: ESP32
# ============================================================

from machine import Pin
import neopixel
import utime

PINO_DADOS = 4
# Pico: PINO_DADOS = 0

NUM_LEDS = 16

np = neopixel.NeoPixel(Pin(PINO_DADOS), NUM_LEDS)

# --- Paleta de cores nomeadas ---
CORES = {
    "vermelho": (255,   0,   0),
    "verde":    (  0, 255,   0),
    "azul":     (  0,   0, 255),
    "amarelo":  (255, 255,   0),
    "ciano":    (  0, 255, 255),
    "magenta":  (255,   0, 255),
    "laranja":  (255, 127,   0),
    "violeta":  (148,   0, 211),
    "branco":   (255, 255, 255),
    "apagado":  (  0,   0,   0),
}

# --- Acende LED 0 em vermelho e LED 8 em azul ---
np[0] = CORES["vermelho"]
np[8] = CORES["azul"]
np.write()

utime.sleep(2)

# --- Acende todos com a mesma cor nomeada ---
for i in range(NUM_LEDS):
    np[i] = CORES["verde"]
np.write()
```

> 💡 **Compare:** `np[0] = CORES["vermelho"]` versus `np[0] = (255, 0, 0)`. O primeiro é imediatamente compreensível — qualquer pessoa que leia o código entende o que acontece.

---

### Parte B — Função com .get() e tratamento de chave inválida

```python
# ============================================================
# Aula 03 — Parte B: função acender_cor() com .get()
# ============================================================

from machine import Pin
import neopixel
import utime

PINO_DADOS = 4
# Pico: PINO_DADOS = 0

NUM_LEDS = 16

np = neopixel.NeoPixel(Pin(PINO_DADOS), NUM_LEDS)

CORES = {
    "vermelho": (255,   0,   0),
    "verde":    (  0, 255,   0),
    "azul":     (  0,   0, 255),
    "amarelo":  (255, 255,   0),
    "ciano":    (  0, 255, 255),
    "magenta":  (255,   0, 255),
    "laranja":  (255, 127,   0),
    "violeta":  (148,   0, 211),
    "branco":   (255, 255, 255),
    "apagado":  (  0,   0,   0),
}

def acender_todos(nome_cor):
    """Acende todos os LEDs com a cor indicada pelo nome.
    Se o nome não existir na paleta, mantém apagado e avisa."""
    cor = CORES.get(nome_cor, None)    # busca o nome; None se não encontrar

    if cor is None:
        print("Cor '{}' não encontrada. Cores disponíveis:".format(nome_cor))
        print(list(CORES.keys()))
        return                         # encerra a função sem acender nada

    for i in range(NUM_LEDS):
        np[i] = cor
    np.write()
    print("Anel aceso em:", nome_cor)

# --- Demonstração ---
acender_todos("vermelho")
utime.sleep(2)

acender_todos("ciano")
utime.sleep(2)

acender_todos("roxo")       # chave inválida — exibe aviso
utime.sleep(2)

acender_todos("apagado")
```

> 💡 **Por que `.get()` e não `CORES[nome_cor]` diretamente?** Com `CORES["roxo"]` o programa trava com `KeyError`. Com `.get("roxo", None)` o programa continua e pode informar o erro ao usuário de forma amigável.

---

### Parte C — Sequência de cores por lista de strings

```python
# ============================================================
# Aula 03 — Parte C: sequência de nomes de cores
# ============================================================

from machine import Pin
import neopixel
import utime

PINO_DADOS = 4
# Pico: PINO_DADOS = 0

NUM_LEDS = 16

np = neopixel.NeoPixel(Pin(PINO_DADOS), NUM_LEDS)

CORES = {
    "vermelho": (255,   0,   0),
    "verde":    (  0, 255,   0),
    "azul":     (  0,   0, 255),
    "amarelo":  (255, 255,   0),
    "ciano":    (  0, 255, 255),
    "magenta":  (255,   0, 255),
    "laranja":  (255, 127,   0),
    "violeta":  (148,   0, 211),
    "branco":   (255, 255, 255),
    "apagado":  (  0,   0,   0),
}

def acender_todos(nome_cor):
    cor = CORES.get(nome_cor, None)
    if cor is None:
        print("Cor '{}' não encontrada.".format(nome_cor))
        return
    for i in range(NUM_LEDS):
        np[i] = cor
    np.write()

# --- Sequência definida como lista de strings ---
sequencia = [
    "vermelho",
    "amarelo",
    "verde",
    "ciano",
    "azul",
    "violeta",
    "apagado",
]

# --- Percorre a sequência e acende cada cor por 1 segundo ---
for nome in sequencia:
    print("Acendendo:", nome)
    acender_todos(nome)
    utime.sleep(1)

print("Sequência concluída.")
```

> 💡 **Perceba a separação de responsabilidades:** a `sequencia` define *o que* acontece; a função `acender_todos()` define *como* acontece. Para mudar a ordem das cores, basta editar a lista — o restante do código não muda.

---

## 4. Circuito Wokwi — diagram.json

Mesmo `diagram.json` das aulas anteriores — nenhuma alteração necessária.

---

## 5. Experimento

Execute a **Parte B** e responda:

**a)** O que o código abaixo retorna? Por quê?

```python
CORES.get("roxo", (0, 0, 0))
```

> _________________________________________________________________

**b)** O que aconteceria se substituísse `.get("roxo", None)` por `CORES["roxo"]` diretamente? Teste e observe a mensagem de erro.

> _________________________________________________________________  
> _________________________________________________________________

**c)** Complete o código para exibir todas as cores disponíveis na paleta:

```python
print(list(CORES._____()))
```

**d)** Na Parte C, adicione `"laranja"` entre `"amarelo"` e `"verde"` na sequência. Quantas linhas do código você precisou alterar?

> _________________________________________________________________

---

## 6. Desafio

**Desafio principal:** expanda a paleta `CORES` com pelo menos 3 cores novas — escolha os nomes e os valores RGB — e crie uma sequência que use todas elas:

```python
CORES["_____"] = (_____, _____, _____)   # sua cor 1
CORES["_____"] = (_____, _____, _____)   # sua cor 2
CORES["_____"] = (_____, _____, _____)   # sua cor 3

sequencia = ["_____", "_____", "_____", "apagado"]

for nome in sequencia:
    acender_todos(nome)
    utime.sleep(_____)
```

**Desafio bônus:** permita que o usuário escolha a cor pelo terminal usando `input()`. O programa lê o nome digitado, busca na paleta e acende o anel — repetindo até o usuário digitar `"sair"`:

```python
print("Cores disponíveis:", list(CORES.keys()))

while True:
    nome = input("Digite o nome da cor (ou 'sair'): ")

    if nome == _____:
        acender_todos("apagado")
        print("Encerrando.")
        break

    acender_todos(_____)
```

---

## Resumo da aula

- Um **dicionário** associa chaves a valores: `{"chave": valor}` — como uma agenda telefônica
- Acesso por chave string: `CORES["vermelho"]` retorna a tupla correspondente
- `.get("chave", padrão)` evita `KeyError` quando a chave pode não existir
- `.keys()` lista todas as chaves disponíveis no dicionário
- Uma **paleta nomeada** torna o código legível e fácil de manter — basta editar o dicionário para mudar as cores em todo o programa

---

*← [Aula 2: Efeitos com Lista](./aula02-efeitos-lista.md) | Próxima → [Aula 4: Efeitos Animados](./aula04-efeitos-animados.md)*
