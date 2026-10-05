---
layout: default
title: Início
---

# Sistemas Embarcados — LEDs, Displays e Efeitos Visuais com ESP32

![Banner do curso](https://rogeriomb-hub.github.io/minicurso_05-embarcados/assets/banner.png)

> Mini curso para o **Curso Técnico em Automação Industrial**  
> Plataforma: ESP32 com MicroPython · Simulador: [Wokwi](https://wokwi.com)

---

## Sobre o curso

Este material ensina, com MicroPython, a controlar saídas visuais no ESP32. A **Seção 1** trabalha LEDs RGB endereçáveis WS2812B (NeoPixel): partindo de um único pixel, o aluno evolui para efeitos com listas, paletas nomeadas com dicionários e animações como spinner, arco-íris, meteoro, respiração e cometa.

A **Seção 2** leva os mesmos recursos de programação — listas, dicionários e tuplas — para a eletrônica digital: codificadores e decodificadores, o display de 7 segmentos e o registrador de deslocamento 74HC595, terminando em um contador de dois dígitos que usa apenas 3 GPIOs.

> **Veio do semáforo?** Se você já fez a Aula 5 do [Mini-curso 01](https://rogeriomb-hub.github.io/minicurso_01-embarcados), vai reconhecer a lógica de temporização e estados — agora aplicada a 16 LEDs coloridos.

---

## Seção 1 — NeoPixel WS2812B

| # | Título | Conceito-chave | Entregável |
|---|--------|----------------|------------|
| [00 ★](https://rogeriomb-hub.github.io/minicurso_05-embarcados/aulas/aula00-extra-tuplas) | **Extra:** Tuplas em Python | `(R, G, B)`, índice, imutabilidade | Apoio opcional para as demais aulas |
| [1](https://rogeriomb-hub.github.io/minicurso_05-embarcados/aulas/aula01-primeiro-pixel) | Primeiro pixel | `neopixel`, tupla RGB, `np.write()` | LED único, todos acesos, `acender_todos()` |
| [2](https://rogeriomb-hub.github.io/minicurso_05-embarcados/aulas/aula02-efeitos-lista) | Efeitos com lista | lista de tuplas, `enumerate()`, `%`, rotação | Spinner, arco-íris fixo |
| [3](https://rogeriomb-hub.github.io/minicurso_05-embarcados/aulas/aula03-paleta-dicionario) | Paleta com dicionário | `dict`, chave string, `.get()`, `.keys()` | Sequência por nome, input interativo |
| [4](https://rogeriomb-hub.github.io/minicurso_05-embarcados/aulas/aula04-efeitos-animados) | Efeitos animados | frames, `sleep_ms`, `//` para brilho | Spinner refinado, arco-íris girante, pulso |
| [5](https://rogeriomb-hub.github.io/minicurso_05-embarcados/aulas/aula05-meteoro-respiracao-cometa) | Meteoro, respiração e cometa | atenuação, `%` circular, lista de intensidades | Meteoro, respiração suave, cometa |

> ★ Aula de apoio — leia antes da Aula 1 se o conceito de tupla ainda não for familiar.

---

## Seção 2 — Display de 7 Segmentos e 74HC595

| # | Título | Conceito-chave | Entregável |
|---|--------|----------------|------------|
| [05 ★](https://rogeriomb-hub.github.io/minicurso_05-embarcados/aulas/aula05-extra-codificadores-decodificadores) | **Extra:** Codificadores e decodificadores | BCD, tabela-verdade, CD4511 e 74LS47 | Codificador e decodificador no terminal |
| [6](https://rogeriomb-hub.github.io/minicurso_05-embarcados/aulas/aula06-display-7-segmentos) | Display de 7 segmentos | catodo × anodo comum, lista, dicionário, tupla de bytes | Contador 0–9 de três formas |
| [7](https://rogeriomb-hub.github.io/minicurso_05-embarcados/aulas/aula07-registrador-74hc595) | Registrador de deslocamento 74HC595 | série → paralelo, clock, latch, cascata, `//` e `%` | Contador 00–20 em dois displays com 3 GPIOs |

> ★ Aula de apoio — leia antes da Aula 6 se *decodificador* ou *BCD* ainda não forem familiares.
> A Seção 2 pode ser feita sem a Seção 1, desde que tuplas ([Aula 00 ★](https://rogeriomb-hub.github.io/minicurso_05-embarcados/aulas/aula00-extra-tuplas)) e dicionários ([Aula 3](https://rogeriomb-hub.github.io/minicurso_05-embarcados/aulas/aula03-paleta-dicionario)) já sejam conhecidos.

---

## Como usar

1. Acesse [wokwi.com](https://wokwi.com) e crie um projeto **ESP32 + MicroPython** (ou **Raspberry Pi Pico + MicroPython** — as diferenças aparecem nos comentários `# Pico:`)
2. Cole o `diagram.json` da aula para montar o circuito automaticamente
3. Copie o código para o editor e execute
4. Responda as perguntas da seção **Experimento** no seu caderno
5. Tente o **Desafio** antes de avançar para a próxima aula

> Os links dos projetos Wokwi prontos estão em [assets/wokwi-links](https://rogeriomb-hub.github.io/minicurso_05-embarcados/assets/wokwi-links).

---

## Pré-requisito

Saber criar um projeto no Wokwi com ESP32 e abrir o editor MicroPython.  
Familiaridade com `for`, funções e listas é recomendada — ou estude o [Mini-curso 01](https://rogeriomb-hub.github.io/minicurso_01-embarcados) primeiro.

---

## Repositório

O código-fonte deste material está disponível no GitHub:

[https://github.com/rogerioMB-hub/minicurso_05-embarcados](https://github.com/rogerioMB-hub/minicurso_05-embarcados)
