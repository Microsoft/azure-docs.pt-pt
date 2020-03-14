---
title: Especificações do Microsoft Azure FXT Edge Filer / Microsoft Docs
description: Especificações físicas e ambientais para hardware Azure FXT Edge Filer
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: c06b0c79e01257eebf566b9752269cb88c072d8a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79264729"
---
# <a name="azure-fxt-edge-filer-specifications"></a>Especificações de filete de borda Azure FXT

Este artigo explica as especificações de hardware para os nós de hardware do Ficheiro de Borda Azure FXT. Na prática, três ou mais nós são configurados em conjunto para fornecer o sistema de cache agrupado.

## <a name="hardware-specifications"></a>Especificações de hardware

| Componente | FXT 6600 | FXT 6400 |
|----------|-----------|-----------|
| Núcleos de CPU |  16 | 16 |
| DRAM  | 1536 GB | 768 GB |
| Portas de rede | 6 x 25/10 Gb + 2 x 1 Gb | 6 x 25/10 Gb + 2 x 1 Gb |
| Capacidade SSD NVMe | 25.6 TB | 12,8 TB |

## <a name="drive-specifications"></a>Especificações de condução

O sistema tem dez compartimentos de condução, acessíveis a partir da frente. Cada unidade povoada é rotulada à direita com informações de capacidade. 

Os números de unidade são impressos no espaço entre unidades. No Azure FXT Edge Filer, a unidade 0 é a unidade superior esquerda e a unidade 1 está diretamente por baixo.

![foto de um compartimento de disco rígido no chassis FXT, mostrando números de unidade e etiquetas de capacidade](media/fxt-drives-photo.png)

| Números de unidade    |  Utilização   |  Especificações |
|------------------|--------|-----------------|
| 0, 1             | SO     | 480 GB SATA SSD |
| 2, 3, 4, 5, 6, 7, 8, 9 | Dados   | FXT 6600: 3.2 TB NVMe SSD <br> FXT 6400: 1.6 TB NVMe SSD |


## <a name="dimensions-and-weight"></a>Dimensões e peso

O Ficheiro de Borda Azure FXT foi concebido para caber num equipamento padrão de 19" e é uma unidade de rack alta (1U). 

<!-- 10x2.5 inches version -->

| Dimensões de filer           |                          |
|-----------------------------|--------------------------|
| Altura                      | 42,8 mm (1,68 polegadas)    |
| Largura (incluindo orelhas de cremalheira) | 482,0 mm (18,97 polegadas)  |
| Largura - recinto principal      | 434,0 mm (17,08 polegadas) |
| Profundidade - orelhas de cremalheira para trás do recinto principal                   | 733,82 mm (29,61 polegadas) |
| Profundidade - orelhas de cremalheira para a mais distante saliência traseira                 | 772,67 mm (30,42 polegadas) |
| Profundidade - orelhas de cremalheira para a mais distante saliência frontal, sem bisel | 22,0 mm (0,87 polegadas)  |
| Profundidade - orelhas de cremalheira para a mais distante saliência frontal, com moldura    | 35,84 mm (1,41 polegadas) |

| Peso | |
|-----------------|----------------------|
| Peso do nó (sem embalagem, sem acessórios) | 40 lbs (18,1 kg) |
| Peso líquido (sem embalagem, incluindo acessórios) | 51 lbs (23,1 kg)|
| Peso bruto (conforme enviado, inclui todas as embalagens) |  64 lbs (29,0 kg) |

### <a name="shipping-dimensions"></a>Dimensões de envio

| Dimensão do pacote | Milímetros | centímetros |
|-------------------|-------------|--------|
| Altura            | 311.2       | 12.25" |
| Pulsação             | 642.8       | 25.31" |
| Comprimento            | 1,051.1     | 41.38" |

## <a name="power-and-thermal-specifications"></a>Especificações de energia e térmicas

Esta secção fornece classificações de potência e medições para o Ficheiro de Borda Azure FXT.

### <a name="nameplate-ratings"></a>Classificações de placade nome

| Classificações de placade identificação para modelos fxt 6000 séries |
|----------------|
| 100 - 240V~    |
| 10A - 5A (X2)  |
| 50/60Hz         |

<!-- matches the Dell regulatory label exactly -->

### <a name="power-and-thermal-measurements"></a>Medições de energia e térmicas 

Os nós de Filer de borda Azure FXT usam ventoinhas de velocidade variável, por isso a potência depende da temperatura e da carga. Velocidades máximas de ventilação podem ser alcançadas em certas combinações de alta carga e temperaturas ambienteelevadas. 

Estes gráficos dão consumo de energia e medições térmicas para combinações de frequência de tensão comumente utilizadas. 

| FxT 6600 potência à temperatura ambiente <br />(22° C, 71,6° F) | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Tensão (V) | 100 | 120 | 208 | 230 | 240 | 
| Frequência (Hz) | 60 | 60 | 60 | 50 | 50 |
| Corrente (A) | 5.02 | 4.16 |2.40 | 2.20 | 2.16 |
| Poder Aparente (VA) | 502 | 499 | 499 | 506 | 518|
| Fator de potência | 0,99 | 0,99 |0.98 | 0.98 | 0.98 |
| Poder Real (W) | 497 |494 | 489 | 496 | 508 |
| Dissipação térmica (BTU/Hr) |1696 | 1686 | 1669 | 1692 | 1733 |

| FxT 6600 potência a velocidades máximas de ventilador | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Tensão (V) | 100 |120 | 208 | 230 | 240| 
| Frequência (Hz) | 60 | 60 | 60 | 50 | 50 |
| Corrente (A) | 5.98 | 5.01 | 2.81 | 2.55 | 2.48 |
| Poder Aparente (VA) | 598 | 601 | 584 | 587 | 595 |
| Fator de potência | 0,99 | 0,99 | 0.98 | 0.98 | 0.98 |
| Poder Real (W) | 592 | 595 | 573 | 575 | 583 |
| Dissipação térmica (BTU/Hr) | 2020 |2031 | 1954 | 1961 | 1990 |

| FxT 6400 potência à temperatura ambiente <br />(22° C, 71,6° F) | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Tensão (V) | 100 | 120 | 208 | 230 | 240 |
| Frequência (Hz) |60 | 60 | 60 | 50 | 50 |
| Corrente (A) | 4.63 | 3.86 | 2.24 | 2.04 | 1.94 |
| Poder Aparente (VA) | 463 | 463 | 466 | 469 | 466 |
| Fator de potência | 0,99 | 0,99 | 0.98 | 0.98 | 0.98 | 
| Poder Real (W) | 458 | 459 | 457 | 460 | 456 |
| Dissipação térmica (BTU/Hr) | 1564 | 1565 | 1558 | 1569 | 1557 |

| FxT 6400 potência a velocidades máximas de ventilador | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz |
|---------|---|---|---|---|---|
| Tensão (V) | 100 | 120 | 208 | 230 | 240 |
| Frequência (Hz) | 60 | 60 | 60 | 50 | 50 |
| Corrente (A) | 5.15 | 4.28 | 2.48 | 2.28 | 2.13 |
| Poder Aparente (VA) | 515 | 514 | 516 | 524 | 511 |
| Fator de potência | 0,99 | 0,99 | 0.98 | 0.98 | 0.98 |
| Poder Real (W) | 510 | 508 | 506 | 514 | 501 |
| Dissipação térmica (BTU/Hr) | 1740 | 1735 | 1725 | 1753 | 1709 |

## <a name="environmental-requirements"></a>Requisitos ambientais

Esta secção fornece especificações para o ambiente ambiente do hardware.

### <a name="temperature-and-humidity"></a>Temperatura e humidade

| Atributo ambiental   | Intervalo de funcionamento                   | Gama não operacional         |
|---------------------------|-----------------------------------|-----------------------------|
| Intervalo de temperatura ambiente | 10°C a 35°C (50 - 86°F)          | -40°C a 65°C (-40 - 149°F) |
| Humidade relativa ambiente | 10% - 80% não condensado          | 5% - 95% não condensação     |
| Ponto máximo de orvalho         | 29°C (84°F)                       | 33°C (91°F)                 |
| Altitude                  | até 3048 metros (10.000 pés), sujeito a desclassificação de temperatura anotada abaixo | até 12.000 metros (39.370 pés) |

> [!NOTE] 
> **Declassificação da temperatura de altitude:** A temperatura máxima é reduzida em 1°C/300 m (1°F/547 ft) acima de 950 m (3,117 pés).

### <a name="airflow-shock-and-vibration"></a>Fluxo de ar, choque e vibração 

| Atributo         | Especificação |
|-------------------|---------------|
| Fluxo de ar                    | O fluxo de ar do sistema é frontal para trás. O sistema deve ser acionado com uma instalação de escape traseira de baixa pressão. |
| Choque, operacional         | 6 G por 11 milissegundos (testado em 6 orientações) |
| Choque, não operacional     | 71 G por 2 milissegundos (testado em 6 orientações) |
| Vibração, operacional     | 0.26 G<sub>RMS</sub> 5 Hz a 350 Hz aleatório         |
| Vibração, não operacional | 1.88 G<sub>RMS</sub> 10 Hz a 500 Hz durante 15 minutos (todos os seis lados testados)  |

## <a name="safety-regulation-compliance"></a>Conformidade da regulamentação da segurança 

Azure FXT Edge Filer cumpre os regulamentos listados. 

| Categoria       | Especificação regulamentar | 
|----------------|--------------------------|
| Segurança geral | EN 60950-1:2006 +A1:2010 +A2:2013 +A11:2009 +A12:2011/IEC 60950-1:2005 ed2 +A1:2009 +A2:2013 <br>EN 62311:2008 | 
| EMC            | FCC A, ICES-003  <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 +A1:2015/CISPR 24:2010 +A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (Classe D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013 |
| Energia         | Regulamento da Comissão (UE) Nº. 617/2013  |
| RoHS           |    EN 50581:2012   |