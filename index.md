---
layout: default
title: Início
---

# Sistemas Embarcados — LEDs RGB e Efeitos Visuais com ESP32

![Banner do curso](https://rogeriomb-hub.github.io/minicurso_05-embarcados/assets/banner.png)

> Mini curso para o **Curso Técnico em Automação Industrial**  
> Plataforma: ESP32 com MicroPython · Simulador: [Wokwi](https://wokwi.com)

---

## Sobre o curso

Este material ensina o controle de LEDs RGB endereçáveis WS2812B (NeoPixel) com MicroPython. Partindo do acendimento de um único pixel, o aluno evolui para efeitos com listas, paletas nomeadas com dicionários e animações como spinner, arco-íris, meteoro, respiração e cometa.

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

## Como usar

1. Acesse [wokwi.com](https://wokwi.com) e crie um projeto **ESP32 + MicroPython**
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
