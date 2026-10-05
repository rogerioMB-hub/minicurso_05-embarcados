# Sistemas Embarcados — Mini Curso: LEDs, Displays e Efeitos Visuais com ESP32

![Banner do curso](https://rogeriomb-hub.github.io/minicurso_05-embarcados/assets/banner.png)

> Estudo dirigido para alunos do Curso Técnico em Automação Industrial.

---

## Sobre o curso

Este material ensina, com MicroPython no ESP32, a controlar saídas visuais. A **Seção 1** trabalha LEDs RGB endereçáveis WS2812B (NeoPixel): partindo de um único pixel, o aluno evolui para efeitos com listas, paletas nomeadas com dicionários e animações como spinner, arco-íris, meteoro, respiração e cometa.

A **Seção 2** aplica listas, dicionários e tuplas à eletrônica digital: codificadores e decodificadores, display de 7 segmentos (catodo e anodo comum) e o registrador de deslocamento 74HC595, terminando em um contador de dois dígitos que usa apenas 3 GPIOs.

O objetivo é aplicar conceitos de programação — listas, tuplas, dicionários, funções e laços — em um contexto visual e motivador, consolidando a base construída nos mini-cursos anteriores.

> **Conexão com o Mini-curso 01:** os alunos que já fizeram o semáforo (Aula 5) vão reconhecer a lógica de estados e temporização — agora aplicada a 16 LEDs coloridos.

---

## Plataforma

| Item | Especificação |
|------|---------------|
| Microcontrolador | ESP32 DevKit (principal) |
| Compatibilidade | Raspberry Pi Pico (instruções nos comentários do código) |
| Linguagem | MicroPython |
| Hardware — Seção 1 | Anel NeoPixel WS2812B — 16 LEDs |
| Hardware — Seção 2 | Display de 7 segmentos de catodo comum (×2), 74HC595 (×2), resistores de 330 Ω |
| Simulador | [Wokwi](https://wokwi.com) — nenhuma instalação necessária |

> **Nota Pico:** sempre que o código diferir para o Raspberry Pi Pico, a linha correspondente aparece em comentário com o prefixo `# Pico:`.

---

## Estrutura do repositório

```
minicurso_05-embarcados/
├── README.md
├── index.md
├── _config.yml
├── COMO-PUBLICAR.md
├── aulas/
│   ├── aula00-extra-tuplas.md          ← pré-requisito opcional
│   ├── aula01-primeiro-pixel.md
│   ├── aula02-efeitos-lista.md
│   ├── aula03-paleta-dicionario.md
│   ├── aula04-efeitos-animados.md
│   ├── aula05-meteoro-respiracao-cometa.md
│   ├── aula05-extra-codificadores-decodificadores.md  ← Seção 2, apoio opcional
│   ├── aula06-display-7-segmentos.md
│   └── aula07-registrador-74hc595.md
└── assets/
    ├── banner.png
    ├── banner.svg
    ├── wokwi-links.md                  ← links dos projetos Wokwi por aula
    ├── 7seg_mapa_segmentos.svg               ← Aulas 05★ e 6: segmentos e bits
    ├── 7seg_catodo_vs_anodo.svg              ← Aula 6: circuito interno
    ├── codificador_decodificador_blocos.svg  ← Aula 05★: tecla → BCD → segmentos
    ├── diagrama_blocos_74hc595_cascata.svg   ← Aula 7: blocos e cascata
    └── deslocamento_byte_0x5B.svg            ← Aula 7: diagrama de tempo
```

---

## Sequência de aulas — Seção 1: NeoPixel

| # | Título | Conceito-chave | Entregável |
|---|--------|----------------|------------|
| [00 ★](./aulas/aula00-extra-tuplas.md) | **Extra:** Tuplas em Python | `(R, G, B)`, índice, imutabilidade | Apoio opcional para as demais aulas |
| [1](./aulas/aula01-primeiro-pixel.md) | Primeiro pixel | `neopixel`, tupla RGB, `np.write()` | LED único aceso, todos acesos, função `acender_todos()` |
| [2](./aulas/aula02-efeitos-lista.md) | Efeitos com lista | lista de tuplas, `enumerate()`, `%`, rotação | Spinner, arco-íris fixo |
| [3](./aulas/aula03-paleta-dicionario.md) | Paleta com dicionário | `dict`, chave string, `.get()`, `.keys()` | Sequência de cores por nome, input interativo |
| [4](./aulas/aula04-efeitos-animados.md) | Efeitos animados | frames, `sleep_ms`, `//` para brilho | Spinner refinado, arco-íris girante, pulso fade in/out |
| [5](./aulas/aula05-meteoro-respiracao-cometa.md) | Meteoro, respiração e cometa | atenuação, `%` circular, lista de intensidades | Meteoro, respiração suave, cometa com cauda colorida |

> ★ Aula de apoio — leia antes da Aula 1 se o conceito de tupla ainda não for familiar.

---

## Sequência de aulas — Seção 2: Display de 7 Segmentos e 74HC595

| # | Título | Conceito-chave | Entregável |
|---|--------|----------------|------------|
| [05 ★](./aulas/aula05-extra-codificadores-decodificadores.md) | **Extra:** Codificadores e decodificadores | BCD, tabela-verdade, CD4511 e 74LS47 | Codificador e decodificador no terminal |
| [6](./aulas/aula06-display-7-segmentos.md) | Display de 7 segmentos | catodo × anodo comum, lista, dicionário, tupla de bytes | Contador 0–9 de três formas |
| [7](./aulas/aula07-registrador-74hc595.md) | Registrador de deslocamento 74HC595 | série → paralelo, clock, latch, cascata, `//` e `%` | Contador 00–20 em dois displays com 3 GPIOs |

> ★ Aula de apoio — leia antes da Aula 6 se *decodificador* ou *BCD* ainda não forem familiares.

> **Conexão com o Mini-curso 01:** a Aula 4 (deslocamento e escrita em porta) cita o display de 7 segmentos como motivação; a Seção 2 mostra isso na prática.

---

## Como usar

1. Acesse [wokwi.com](https://wokwi.com) e crie um projeto com **ESP32** e **MicroPython**.
2. Cole o `diagram.json` da aula no projeto para montar o circuito automaticamente.
3. Copie o código da seção **Código** para o editor do Wokwi.
4. Execute, observe e responda as perguntas da seção **Experimento**.
5. Tente o **Desafio** antes de passar para a próxima aula.

---

## Pré-requisito

Saber criar um projeto no Wokwi com ESP32 e abrir o editor MicroPython.  
Familiaridade com `for`, funções e listas em Python é recomendada — ou estude as aulas do [Mini-curso 01](https://rogeriomb-hub.github.io/minicurso_01-embarcados) primeiro.

---

## Material publicado

- **GitHub Pages:** [https://rogeriomb-hub.github.io/minicurso_05-embarcados](https://rogeriomb-hub.github.io/minicurso_05-embarcados)
- **Google Sites:** distribuído pelo professor em sala de aula.
