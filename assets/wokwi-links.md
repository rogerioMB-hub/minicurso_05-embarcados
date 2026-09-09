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

### ESP32 (principal)

| Função | GPIO | Aulas |
|--------|:----:|-------|
| Anel NeoPixel — DIN (dados) | 4 | 1, 2, 3, 4, 5 |
| Anel NeoPixel — VCC | 3.3 V | 1, 2, 3, 4, 5 |
| Anel NeoPixel — GND | GND | 1, 2, 3, 4, 5 |

### Raspberry Pi Pico (alternativa)

| Função | GPIO |
|--------|:----:|
| Anel NeoPixel — DIN (dados) | 0 |
| Anel NeoPixel — VCC | 3.3 V |
| Anel NeoPixel — GND | GND |

> Para o Pico, selecione **Raspberry Pi Pico** ao criar o projeto no Wokwi.
> No código, troque `PINO_DADOS = 4` por `PINO_DADOS = 0` (linha marcada com `# Pico:`).

---

## Circuito — diagram.json (válido para todas as aulas)

> **Atenção:** o `diagram.json` abaixo é o circuito base de todo o mini-curso.
> O mesmo arquivo é reutilizado da Aula 1 à Aula 5 — nenhuma alteração necessária.
> Valide no Wokwi antes de publicar: execute a Parte A da Aula 1 e confirme que o LED 0 acende em vermelho.

```json
{
  "version": 1,
  "author": "RMB - Mini Curso Embarcados 05",
  "editor": "wokwi",
  "parts": [
    {
      "type": "wokwi-esp32-devkit-v1",
      "id": "esp",
      "top": 0,
      "left": 0,
      "attrs": {}
    },
    {
      "type": "wokwi-neopixel-ring",
      "id": "ring1",
      "top": 80,
      "left": 220,
      "attrs": { "pixels": "16" }
    }
  ],
  "connections": [
    [ "ring1:DIN", "esp:4",   "green", [] ],
    [ "ring1:VCC", "esp:3V3", "red",   [] ],
    [ "ring1:GND", "esp:GND", "black", [] ]
  ]
}
```

---

## Links dos projetos Wokwi por aula

> Crie cada projeto no Wokwi, salve e substitua os links abaixo pelos links reais gerados.

| Aula | Título | Link Wokwi |
|------|--------|------------|
| 00-extra | Tuplas em Python | — (sem circuito) |
| 1 | Primeiro Pixel | *(a publicar após validação)* |
| 2 | Efeitos com Lista | *(mesmo projeto da Aula 1)* |
| 3 | Paleta com Dicionário | *(mesmo projeto da Aula 1)* |
| 4 | Efeitos Animados | *(mesmo projeto da Aula 1)* |
| 5 | Meteoro, Respiração e Cometa | *(mesmo projeto da Aula 1)* |

> Como todas as aulas usam o mesmo circuito, um único projeto Wokwi salvo é suficiente para todo o mini-curso.

---

## Restrições importantes do Wokwi

| Situação | Problema | Solução |
|----------|----------|---------|
| Tensão do anel | WS2812B especifica 5 V | No Wokwi, 3.3 V funciona; em hardware real, use fonte 5 V externa |
| Brilho máximo com muitos LEDs | Consumo elevado | No Wokwi sem limitação; em hardware real, limitar brilho ou usar fonte dedicada |
| Circuitos gerados automaticamente | Conexões frequentemente incompletas | Validar no Wokwi antes de publicar |
| `input()` no terminal Wokwi | Funciona normalmente | Use para a Aula 3 (bônus com input interativo) |

---

## Referências

- [Documentação MicroPython — NeoPixel](https://docs.micropython.org/en/latest/library/neopixel.html)
- [Documentação MicroPython — machine.Pin](https://docs.micropython.org/en/latest/library/machine.Pin.html)
- [Wokwi — NeoPixel Ring](https://docs.wokwi.com/pt-BR/parts/wokwi-neopixel-ring)
- [Wokwi ESP32 + MicroPython](https://docs.wokwi.com/pt-BR/guides/micropython)
- [Wokwi Raspberry Pi Pico](https://docs.wokwi.com/pt-BR/parts/wokwi-pi-pico)
- [WS2812B Datasheet](https://cdn-shop.adafruit.com/datasheets/WS2812B.pdf)
