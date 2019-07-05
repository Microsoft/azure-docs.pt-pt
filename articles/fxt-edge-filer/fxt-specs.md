---
title: Especificações de filtro de borda do Microsoft Azure FXT | Documentos da Microsoft
description: Físicas e ambientais especificações de hardware de ficheiros do Azure FXT Edge
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: overview
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: 0679bce8eae515aa6b90e34fcfd15ee9b4e56b31
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542877"
---
# <a name="azure-fxt-edge-filer-specifications"></a>Especificações de FXT Edge ficheiros do Azure

Este artigo explica as especificações de hardware para nós de hardware de ficheiros do Azure FXT Edge. Na prática, três ou mais nós configurados em conjunto para fornecer o sistema de cache em cluster.

## <a name="hardware-specifications"></a>Especificações de hardware

| Componente | FXT 6600 | FXT 6400 |
|----------|-----------|-----------|
| Núcleos de CPU |  16 | 16 |
| DRAM  | 1536 GB | 768 GB |
| Portas de rede | 6 x 25/10 Gb + 2 x 1 Gb | 6 x 25/10 Gb + 2 x 1 Gb |
| Capacidade de NVMe SSD | 25.6 TB | 12.8 TB |

## <a name="drive-specifications"></a>Especificações de unidade

O sistema tem dez compartimentos de unidade, acessíveis a partir do início. Cada unidade preenchida é rotulado como à direita com informações de capacidade. 

Números de unidade são impressos no espaço de entre unidades. O filtro de borda do Azure FXT, unidade 0 é a parte superior esquerda da unidade e unidade 1 está diretamente abaixo dela.

![fotografia de um disco rígido cais no chassi FXT, que mostra a unidade de números e capacidade de etiquetas](media/fxt-drives-photo.png)

| Números de unidade    |  Utilizar   |  Especificações |
|------------------|--------|-----------------|
| 0, 1             | OS     | 480 GB SATA SSD |
| 2, 3, 4, 5, 6, 7, 8, 9 | Data   | FXT 6600: 3.2 TB NVMe SSD <br> FXT 6400: 1.6 TB NVMe SSD |


## <a name="dimensions-and-weight"></a>Dimensões e peso

O filtro de borda de FXT do Azure foi concebido para caber num bastidor de equipamento padrão 19" e é um rack unidade elevada (1U). 

<!-- 10x2.5 inches version -->

| Dimensões de filtro           |                          |
|-----------------------------|--------------------------|
| Altura                      | mm 42.8 (1.68 polegadas)    |
| Largura (incluindo ouvidos rack) | mm 482.0 (18.97 polegadas)  |
| Largura - bastidor principal      | mm 434.0 (17.08 polegadas) |
| Profundidade - ouvidos de rack para inverso de bastidor principal                   | mm 733.82 (29.61 polegadas) |
| Profundidade - ouvidos de rack para furthest rear protrusion                 | mm 772.67 (30.42 polegadas) |
| Profundidade - ouvidos de rack para protrusion front-furthest, sem bezel | mm 22.0 (0.87 polegada)  |
| Profundidade - ouvidos de rack para protrusion front-furthest, com bezel    | mm 35.84 (1.41 polegadas) |

| Peso | |
|-----------------|----------------------|
| Peso de nó (sem o empacotamento, sem acessórios) | 40 lbs (18.1 kg) |
| Peso da NET (sem o empacotamento, incluindo acessórios) | 51 lbs (23.1 kg)|
| Peso bruto (tal como fornecido, inclui todos os empacotamento) |  64 lbs (29.0 kg) |

### <a name="shipping-dimensions"></a>Dimensões de envio

| Dimensão do pacote | Milímetros | Polegadas |
|-------------------|-------------|--------|
| Altura            | 311.2       | 12.25" |
| Largura             | 642.8       | 25.31" |
| Comprimento            | 1,051.1     | 41.38" |

## <a name="power-and-thermal-specifications"></a>Poder e especificações térmicos

Esta secção fornece as classificações de energia e medidas para o filtro de borda de FXT do Azure.

### <a name="nameplate-ratings"></a>Classificações de nameplate

| Classificações de nameplate para modelos de série de FXT 6000 |
|----------------|
| 100 - 240V ~    |
| 10A - 5A (X2)  |
| 50/60Hz         |

<!-- matches the Dell regulatory label exactly -->

### <a name="power-and-thermal-measurements"></a>Poder e medidas térmicos 

Nós de FXT Edge ficheiros do Azure utilizam fãs de velocidade de variável, a energia depende de temperatura e a carga. Ventoinha máximo velocidades podem ser contatadas em determinados combinações de carga elevada e temperaturas ambiente elevadas. 

Estes gráficos dar o consumo de energia e medidas de saída térmicos para combinações de frequência de tensão comumente utilizados. 

| Power FXT 6600 em temperatura ambiente <br />(22° C, 71.6° F) | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Tensão (V) | 100 | 120 | 208 | 230 | 240 | 
| Frequência (Hz) | 60 | 60 | 60 | 50 | 50 |
| Atual (A) | 5.02 | 4.16 |2.40 | 2.20 | 2.16 |
| (VA potência aparente) | 502 | 499 | 499 | 506 | 518|
| Fator de potência | 0,99 | 0,99 |0.98 | 0.98 | 0.98 |
| Potência real (W) | 497 |494 | 489 | 496 | 508 |
| Dissipação térmicos (BTU/hora) |1696 | 1686 | 1669 | 1692 | 1733 |

| Power FXT 6600 em velocidades de ventoinha máximo | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Tensão (V) | 100 |120 | 208 | 230 | 240| 
| Frequência (Hz) | 60 | 60 | 60 | 50 | 50 |
| Atual (A) | 5.98 | 5.01 | 2.81 | 2.55 | 2.48 |
| (VA potência aparente) | 598 | 601 | 584 | 587 | 595 |
| Fator de potência | 0,99 | 0,99 | 0.98 | 0.98 | 0.98 |
| Potência real (W) | 592 | 595 | 573 | 575 | 583 |
| Dissipação térmicos (BTU/hora) | 2020 |2031 | 1954 | 1961 | 1990 |

| Power FXT 6400 em temperatura ambiente <br />(22° C, 71.6° F) | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Tensão (V) | 100 | 120 | 208 | 230 | 240 |
| Frequência (Hz) |60 | 60 | 60 | 50 | 50 |
| Atual (A) | 4.63 | 3.86 | 2.24 | 2.04 | 1.94 |
| (VA potência aparente) | 463 | 463 | 466 | 469 | 466 |
| Fator de potência | 0,99 | 0,99 | 0.98 | 0.98 | 0.98 | 
| Potência real (W) | 458 | 459 | 457 | 460 | 456 |
| Dissipação térmicos (BTU/hora) | 1564 | 1565 | 1558 | 1569 | 1557 |

| Power FXT 6400 em velocidades de ventoinha máximo | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz |
|---------|---|---|---|---|---|
| Tensão (V) | 100 | 120 | 208 | 230 | 240 |
| Frequência (Hz) | 60 | 60 | 60 | 50 | 50 |
| Atual (A) | 5.15 | 4.28 | 2.48 | 2.28 | 2.13 |
| (VA potência aparente) | 515 | 514 | 516 | 524 | 511 |
| Fator de potência | 0,99 | 0,99 | 0.98 | 0.98 | 0.98 |
| Potência real (W) | 510 | 508 | 506 | 514 | 501 |
| Dissipação térmicos (BTU/hora) | 1740 | 1735 | 1725 | 1753 | 1709 |

## <a name="environmental-requirements"></a>Requisitos de ambientais

Esta secção fornece especificações para o ambiente de ambiente do hardware.

### <a name="temperature-and-humidity"></a>Temperatura e humidade

| Atributo ambiental   | Intervalo de funcionamento                   | Intervalo de não-operacional         |
|---------------------------|-----------------------------------|-----------------------------|
| Intervalo de temperatura ambiente | 10° C, 35° c (50-86° F)          | -40,Year),temperature ° C 65 ° c (-40,Year),temperature-149 ° F) |
| Ambiente de caminho relativo humidade | 10% a 80% - não-condensar          | 5% a 95% - não-condensar     |
| Ponto de orvalho máximo         | 29°C (84°F)                       | 33°C (91°F)                 |
| Altitude                  | até 3048 medidores (pés 10 000), sujeito a temperatura anular classificação indicado abaixo | Medidores até 12 000 (39,370 metros) |

> [!NOTE] 
> **Em altitude temperatura anular classificação:** Temperatura máxima é reduzida por 1° C/300 m (1° F/547 quadrado) acima 950 m (3,117 quadrado).

### <a name="airflow-shock-and-vibration"></a>Ventilação, choque e vibração 

| Atributo         | Especificação |
|-------------------|---------------|
| Ventilação                    | Ventilação do sistema é, de frente para trás. Sistema tem de ser utilizado com uma instalação de esgotar rear low-pressure. |
| Choque, operacional         | 6 G para 11 milissegundos (testado em 6 orientações) |
| Choque, inoperacional     | G 71 por 2 milissegundos (testado em 6 orientações) |
| Vibração, operacional     | 0.26 G<sub>RMS</sub> Hz 5 para Hz 350 aleatório         |
| Vibração, inoperacional | 1.88 G<sub>RMS</sub> Hz 10 a 500 Hz para 15 minutos (todos os seis lados testados)  |

## <a name="safety-regulation-compliance"></a>Conformidade de regulamentação de segurança 

Filtro de borda FXT do Azure está em conformidade com as regulamentações listadas. 

| Category       | Especificação de regulamentação | 
|----------------|--------------------------|
| Segurança geral | EN 60950-1:2006 +A1:2010 +A2:2013 +A11:2009 +A12:2011/IEC 60950-1:2005 ed2 +A1:2009 +A2:2013 <br>EN 62311:2008 | 
| EMC            | FCC A, ICES-003  <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 +A1:2015/CISPR 24:2010 +A1:2015  <br>EN 61000-3-2:2014 / IEC 61000-3-2:2014 (classe D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013 |
| Energia         | Não. de Regulamento (UE) Commission 617/2013  |
| RoHS           |    EN 50581:2012   |