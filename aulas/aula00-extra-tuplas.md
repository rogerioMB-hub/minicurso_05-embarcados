---
layout: default
title: "Aula 00-extra — Tuplas em Python"
---

# Aula 00-extra — Tuplas em Python

> **Duração estimada:** 15 minutos  
> **Leia antes de:** Aula 1 — Primeiro Pixel

---

## Objetivo

Ao final desta leitura você será capaz de:

- Reconhecer a sintaxe de uma tupla em Python
- Entender por que tuplas são usadas para representar cores RGB
- Acessar elementos de uma tupla por índice
- Distinguir tupla de lista na prática

---

## 1. Conceito

### O que é uma tupla?

Uma **tupla** é um grupo de valores relacionados, escritos entre parênteses e separados por vírgulas:

```python
coordenada = (10, 25)           # posição x, y
data       = (15, 6, 2025)      # dia, mês, ano
cor        = (255, 0, 0)        # vermelho: R=255, G=0, B=0
```

Pense numa tupla como uma **caixinha selada**: ela guarda um conjunto fixo de valores que pertencem juntos e não se separam.

### Acessando elementos por índice

Assim como em uma lista, você acessa cada elemento pelo seu índice — começando em `0`:

```python
cor = (255, 128, 0)   # laranja

print(cor[0])         # → 255  (canal vermelho)
print(cor[1])         # → 128  (canal verde)
print(cor[2])         # → 0    (canal azul)
```

Você também pode desempacotar a tupla em variáveis separadas:

```python
r, g, b = cor
print(r)   # → 255
print(g)   # → 128
print(b)   # → 0
```

Esse recurso aparece bastante no código das aulas — por exemplo, ao ler a cor atual de um LED:

```python
r, g, b = np[i]   # desempacota a tupla do LED i
```

### Tupla vs. lista — qual a diferença prática?

A diferença principal é a **imutabilidade**: depois de criada, uma tupla não pode ser alterada.

```python
lista = [255, 0, 0]
lista[0] = 100        # ✔ funciona — lista aceita alteração

cor = (255, 0, 0)
cor[0] = 100          # ✘ erro — tupla não pode ser alterada
```

| | Lista `[]` | Tupla `()` |
|---|---|---|
| Sintaxe | `[a, b, c]` | `(a, b, c)` |
| Pode alterar elementos? | Sim | Não |
| Uso típico | Sequência que muda | Grupo fixo de valores |
| Exemplo | Lista de LEDs a acender | Cor RGB `(255, 0, 0)` |

> **Por que usar tupla para cor?** Uma cor `(255, 0, 0)` é um dado fixo — vermelho não vira verde no meio do programa. A imutabilidade da tupla protege esse valor de alterações acidentais e sinaliza ao leitor que aqueles três números pertencem juntos, nessa ordem.

### Tupla com um único elemento

Atenção: uma tupla com um elemento precisa de vírgula — sem ela, Python interpreta como apenas um valor entre parênteses:

```python
nao_e_tupla = (42)    # isso é só o número 42
e_tupla      = (42,)  # isso é uma tupla com um elemento
```

Nas aulas deste curso todas as tuplas têm três elementos (R, G, B), então esse caso não aparece — mas é bom saber.

---

## 2. Tuplas de cor na prática

Veja como as tuplas aparecem ao longo das aulas:

**Aula 1 — atribuição direta:**
```python
np[0] = (255, 0, 0)       # acende o LED 0 em vermelho
```

**Aula 2 — lista de tuplas:**
```python
paleta = [
    (255,   0,   0),       # vermelho
    (255, 127,   0),       # laranja
    (  0, 255,   0),       # verde
]
np[0] = paleta[0]          # acende o LED 0 com a primeira cor da paleta
```

**Aula 3 — dicionário de tuplas:**
```python
CORES = {
    "vermelho": (255,   0,   0),
    "verde":    (  0, 255,   0),
}
np[0] = CORES["vermelho"]  # acende o LED 0 em vermelho pelo nome
```

**Aulas 4 e 5 — desempacotamento:**
```python
r, g, b = np[i]            # lê a cor atual do LED i
np[i] = (r // 2, g // 2, b // 2)   # reduz o brilho pela metade
```

---

## 3. Experimento rápido

Teste os exemplos abaixo diretamente no terminal MicroPython (REPL) do Wokwi — sem precisar de circuito:

**a)** Crie uma tupla de cor e acesse cada canal:

```python
cor = (0, 200, 100)
print("R =", cor[0])
print("G =", cor[1])
print("B =", cor[2])
```

**b)** Desempacote a tupla e some os canais:

```python
r, g, b = cor
total = r + g + b
print("Brilho total:", total)   # qual valor você espera?
```

**c)** Tente alterar um elemento e observe o erro:

```python
cor[0] = 255    # o que acontece?
```

> Anote a mensagem de erro: ___________________________________

---

## Resumo

- Tupla é um grupo fixo de valores entre parênteses: `(a, b, c)`
- Acesso por índice: `cor[0]` → canal vermelho
- Imutável: não pode ser alterada após criada
- Cores RGB são representadas como tuplas `(R, G, B)` com valores de 0 a 255
- Desempacotamento `r, g, b = cor` separa os canais em variáveis individuais

---

*← [Início](../index.md) | Próxima → [Aula 1: Primeiro Pixel](./aula01-primeiro-pixel.md)*
