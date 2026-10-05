---
layout: default
title: "Referências e Links Wokwi"
---

# Referências e Links para o Wokwi

## Como criar um projeto no Wokwi

1. Acesse [wokwi.com](https://wokwi.com)
2. Clique em **New Project**
3. Selecione **ESP32** → **MicroPython**
4. Cole o `diagram.json` da aula na aba **Diagram**
5. Cole o código na aba **main.py**
6. Clique em **Play** para simular

---

## Mapeamento de pinos por aula

### Seção 1 — ESP32 (principal)

| Função | GPIO | Aulas |
|--------|:----:|-------|
| Anel NeoPixel — DIN (dados) | 4 | 1, 2, 3, 4, 5 |
| Anel NeoPixel — VCC | 5 V (hardware real) | 1, 2, 3, 4, 5 |
| Anel NeoPixel — GND | GND | 1, 2, 3, 4, 5 |

### Seção 1 — Raspberry Pi Pico (alternativa)

| Função | GPIO |
|--------|:----:|
| Anel NeoPixel — DIN (dados) | 0 |
| Anel NeoPixel — VCC | 5 V (hardware real) |
| Anel NeoPixel — GND | GND |

> Para o Pico, selecione **Raspberry Pi Pico** ao criar o projeto no Wokwi.
> No código, troque `PINO_DADOS = 4` por `PINO_DADOS = 0` (linha marcada com `# Pico:`).


### Seção 2 — Aula 6: display de 7 segmentos (catodo comum)

| Segmento | a | b | c | d | e | f | g | dp | COM |
|---|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| ESP32 | 23 | 22 | 21 | 19 | 18 | 25 | 26 | 27 | GND |
| Pico | GP0 | GP1 | GP2 | GP3 | GP4 | GP5 | GP6 | GP7 | GND |

### Seção 2 — Aula 7: 74HC595 em cascata

| Sinal | 74HC595 | ESP32 | Pico |
|---|---|:-:|:-:|
| Dados | U1 · DS | 23 | GP19 |
| Clock | U1 e U2 · SHCP | 18 | GP18 |
| Latch | U1 e U2 · STCP | 21 | GP17 |
| Cascata | U1 · Q7S → U2 · DS | — | — |
| OE / MR / VCC | GND / 3,3 V / 3,3 V | — | — |

> Todos os GPIOs da Seção 2 no ESP32 evitam pinos de boot (0, 2, 5, 12, 15), da memória flash (6–11), da PSRAM em módulos WROVER (16, 17) e os somente-entrada (34–39).

---

## Seção 1 — Circuito NeoPixel (diagram.json válido para as Aulas 1 a 5)

> O mesmo arquivo é reutilizado da Aula 1 à Aula 5 — nenhuma alteração necessária.
> ✅ Circuito validado — é o mesmo do projeto [wokwi.com/projects/474715111472158721](https://wokwi.com/projects/474715111472158721).

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

---

## Seção 2 — Circuitos

O `diagram.json` completo de cada aula está na própria aula, na seção **4. Circuito Wokwi**:

- [Aula 6 — um display de 7 segmentos](https://rogeriomb-hub.github.io/minicurso_05-embarcados/aulas/aula06-display-7-segmentos) (`wokwi-esp32-devkit-v1` + `wokwi-7segment`)
- [Aula 7 — dois 74HC595 e dois displays](https://rogeriomb-hub.github.io/minicurso_05-embarcados/aulas/aula07-registrador-74hc595) (`wokwi-esp32-devkit-v1` + 2 × `wokwi-74hc595` + 2 × `wokwi-7segment`)

> ⚠️ **Validar antes de publicar** — os dois circuitos foram conferidos pino a pino, mas ainda precisam ser abertos no Wokwi e salvos como projeto; depois substitua os "a criar" da tabela abaixo pelos links reais.

---

## Links dos projetos Wokwi por aula

> Crie cada projeto no Wokwi, salve e substitua os links abaixo pelos links reais gerados.

| Aula | Título | Link Wokwi |
|------|--------|------------|
| 00-extra | Tuplas em Python | — (sem circuito) |
| 1 | Primeiro Pixel | [wokwi.com/projects/474715111472158721](https://wokwi.com/projects/474715111472158721) |
| 2 | Efeitos com Lista | mesmo projeto da Aula 1 |
| 3 | Paleta com Dicionário | mesmo projeto da Aula 1 |
| 4 | Efeitos Animados | mesmo projeto da Aula 1 |
| 5 | Meteoro, Respiração e Cometa | mesmo projeto da Aula 1 |
| 05-extra | Codificadores e Decodificadores | — (sem circuito, só terminal) |
| 6 | Display de 7 Segmentos | a criar — `diagram.json` da Aula 6 |
| 7 | Registrador 74HC595 | a criar — `diagram.json` da Aula 7 |

> Na Seção 1 todas as aulas usam o mesmo circuito, então um único projeto Wokwi basta. Na Seção 2 são dois projetos: um para a Aula 6 e outro para a Aula 7.

---

## Restrições importantes do Wokwi

| Situação | Problema | Solução |
|----------|----------|---------|
| Tensão do anel | WS2812B especifica 5 V | Wokwi usa pino 3V3 por limitação do simulador; em hardware real, use 5 V no VCC do anel |
| Brilho máximo com muitos LEDs | Consumo elevado | No Wokwi sem limitação; em hardware real, limitar brilho ou usar fonte dedicada |
| Circuitos gerados automaticamente | Conexões frequentemente incompletas | Validar no Wokwi antes de publicar |
| `input()` no terminal Wokwi | Funciona normalmente | Use para a Aula 3 (bônus com input interativo) |
| Display de 7 segmentos | O padrão do `wokwi-7segment` é **anodo** comum | Use `"common": "cathode"` nos `attrs` (já presente nos circuitos da Seção 2) |
| Resistores dos segmentos | Wokwi não queima LEDs | Omitidos no simulador; **obrigatórios** na bancada (330 Ω por segmento) |
| Tensão do 74HC595 | Em 5 V exige ≥ 3,15 V na entrada | Alimente com 3,3 V junto do ESP32/Pico, ou use 74HCT595 em 5 V |

---

## Referências

- [Documentação MicroPython — NeoPixel](https://docs.micropython.org/en/latest/library/neopixel.html)
- [Documentação MicroPython — machine.Pin](https://docs.micropython.org/en/latest/library/machine.Pin.html)
- [Wokwi — NeoPixel Ring](https://docs.wokwi.com/pt-BR/parts/wokwi-neopixel-ring)
- [Wokwi ESP32 + MicroPython](https://docs.wokwi.com/pt-BR/guides/micropython)
- [Wokwi Raspberry Pi Pico](https://docs.wokwi.com/pt-BR/parts/wokwi-pi-pico)
- [WS2812B Datasheet](https://cdn-shop.adafruit.com/datasheets/WS2812B.pdf)
- [Wokwi — display de 7 segmentos](https://docs.wokwi.com/parts/wokwi-7segment) · [Wokwi — 74HC595](https://docs.wokwi.com/parts/wokwi-74hc595)
- [Datasheet TI SN74HC595](https://www.ti.com/lit/ds/symlink/sn74hc595.pdf)
- [Display catodo comum Kingbright SC56-11EWA](https://www.kingbrightusa.com/images/catalog/SPEC/SC56-11EWA.pdf) · [anodo comum SA56-11EWA](https://www.kingbrightusa.com/images/catalog/SPEC/SA56-11EWA.pdf)
- [Decodificadores TI CD4511B](https://www.ti.com/lit/ds/symlink/cd4511b.pdf) · [SN74LS47](https://www.ti.com/lit/ds/symlink/sn74ls47.pdf)
