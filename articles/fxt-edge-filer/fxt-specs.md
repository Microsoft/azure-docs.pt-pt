---
title: Microsoft Azure especificações do FXT Edge Filer | Microsoft Docs
description: Especificações físicas e ambientais para o hardware de filer do Azure FXT Edge
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: overview
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: 90d56ad0cec3d187d4c7307f8c004b14c3860390
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72254704"
---
# <a name="azure-fxt-edge-filer-specifications"></a>Especificações do Azure FXT Edge para o arquivo

Este artigo explica as especificações de hardware para nós de hardware do Azure FXT Edge Filer. Na prática, três ou mais nós são configurados juntos para fornecer o sistema de cache clusterizado.

## <a name="hardware-specifications"></a>Especificações de hardware

| Componente | FXT 6600 | FXT 6400 |
|----------|-----------|-----------|
| Núcleos de CPU |  16 | 16 |
| DRAM  | 1536 GB | 768 GB |
| Portas de rede | 6 x 25/10 GB + 2 x 1 GB | 6 x 25/10 GB + 2 x 1 GB |
| Capacidade SSD de NVMe | 25,6 TB | 12,8 TB |

## <a name="drive-specifications"></a>Especificações da unidade

O sistema tem dez baias de unidade, acessível da frente. Cada unidade populada é rotulada à direita com informações de capacidade. 

Os números de unidade são impressos no espaço entre as unidades. No arquivo de borda do Azure FXT Edge, a unidade 0 é a unidade superior esquerda e a unidade 1 está diretamente abaixo dela.

![foto de um compartimento de disco rígido no chassi FXT, mostrando números de unidade e rótulos de capacidade](media/fxt-drives-photo.png)

| Números de unidade    |  Usar   |  Especificações |
|------------------|--------|-----------------|
| 0, 1             | SO     | SSD SATA DE 480 GB |
| 2, 3, 4, 5, 6, 7, 8, 9 | Dados   | FXT 6600:3,2 TB de SSD do NVMe <br> FXT 6400:1,6 TB de SSD do NVMe |


## <a name="dimensions-and-weight"></a>Dimensões e peso

O filer do Azure FXT Edge foi projetado para se ajustar a um rack de equipamento padrão de 19 e é uma unidade de rack alta (1U). 

<!-- 10x2.5 inches version -->

| Dimensões de filer           |                          |
|-----------------------------|--------------------------|
| Tamanho                      | 42,8 mm (1,68 polegadas)    |
| Largura (incluindo ouvidos em rack) | 482,0 mm (18,97 polegadas)  |
| Largura-compartimento principal      | 434,0 mm (17, 8 polegadas) |
| Os ouvidos do rack de profundidade para trás do compartimento principal                   | 733,82 mm (29,61 polegadas) |
| Ouvidos em rack de profundidade para protrusion de fundo mais distantes                 | 772,67 mm (30,42 polegadas) |
| Os ouvidos do rack de profundidade para o protrusion frontal mais distante, sem o painel | 22,0 mm (0,87 polegadas)  |
| Os ouvidos do rack de profundidade para o protrusion frontal mais distante, com o painel    | 35,84 mm (1,41 polegadas) |

| Peso | |
|-----------------|----------------------|
| Peso do nó (sem empacotamento, sem acessórios) | 40 kg (18,1 lb) |
| Peso líquido (sem empacotamento, incluindo acessórios) | 51 kg (23,1 lb)|
| Peso bruto (conforme fornecido, inclui todo o empacotamento) |  64 kg (29,0 lb) |

### <a name="shipping-dimensions"></a>Dimensões de remessa

| Dimensão do pacote | Milímetros | 5,08 |
|-------------------|-------------|--------|
| Tamanho            | 311,2       | 12,25 " |
| Largura             | 642,8       | 25,31 " |
| Comprimento            | 1051,1     | 41,38 " |

## <a name="power-and-thermal-specifications"></a>Especificações de energia e térmicas

Esta seção fornece classificações de energia e medidas para o filer do Azure FXT Edge.

### <a name="nameplate-ratings"></a>Classificações de placa

| Placa classificações para modelos da série FXT 6000 |
|----------------|
| 100-240V ~    |
| 10A-5A (X2)  |
| 50/60Hz         |

<!-- matches the Dell regulatory label exactly -->

### <a name="power-and-thermal-measurements"></a>Medidas de energia e térmicas 

Os nós de filer do Azure FXT Edge usam ventiladores de velocidade variável, portanto, a energia depende da temperatura e da carga. As velocidades máximas do ventilador podem ser alcançadas em determinadas combinações de alta carga e temperaturas de ambiente elevadas. 

Esses gráficos fornecem o consumo de energia e medidas de saída térmica para combinações de frequência de tensão usadas com frequência. 

| Energia de FXT 6600 na temperatura da sala <br />(22 ° C, 71,6 ° F) | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Tensão (V) | 100 | 120 | 208 | 230 | 240 | 
| Frequência (Hz) | 60 | 60 | 60 | 50 | 50 |
| Atual (A) | 5, 2 | 4,16 |2,40 | 2,20 | 2,16 |
| Potência aparente (VA) | 502 | 499 | 499 | 506 | 518|
| Fator de potência | 0,99 | 0,99 |0,98 | 0,98 | 0,98 |
| Energia real (W) | 497 |494 | 489 | 496 | 508 |
| Dissipação térmica (BTU/h) |1696 | 1686 | 1669 | 1692 | 1733 |

| Energia de FXT 6600 em velocidades de ventilador máximas | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Tensão (V) | 100 |120 | 208 | 230 | 240| 
| Frequência (Hz) | 60 | 60 | 60 | 50 | 50 |
| Atual (A) | 5,98 | 5, 1 | 2,81 | 2,55 | 2,48 |
| Potência aparente (VA) | 598 | 601 | 584 | 587 | 595 |
| Fator de potência | 0,99 | 0,99 | 0,98 | 0,98 | 0,98 |
| Energia real (W) | 592 | 595 | 573 | 575 | 583 |
| Dissipação térmica (BTU/h) | 2020 |2031 | 1954 | 1961 | 1990 |

| Energia de FXT 6400 na temperatura da sala <br />(22 ° C, 71,6 ° F) | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Tensão (V) | 100 | 120 | 208 | 230 | 240 |
| Frequência (Hz) |60 | 60 | 60 | 50 | 50 |
| Atual (A) | 4,63 | 3,86 | 2,24 | 2, 4 | 1,94 |
| Potência aparente (VA) | 463 | 463 | 466 | 469 | 466 |
| Fator de potência | 0,99 | 0,99 | 0,98 | 0,98 | 0,98 | 
| Energia real (W) | 458 | 459 | 457 | 460 | 456 |
| Dissipação térmica (BTU/h) | 1564 | 1565 | 1558 | 1569 | 1557 |

| Energia de FXT 6400 em velocidades de ventilador máximas | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz |
|---------|---|---|---|---|---|
| Tensão (V) | 100 | 120 | 208 | 230 | 240 |
| Frequência (Hz) | 60 | 60 | 60 | 50 | 50 |
| Atual (A) | 5,15 | 4,28 | 2,48 | 2,28 | 2,13 |
| Potência aparente (VA) | 515 | 514 | 516 | 524 | 511 |
| Fator de potência | 0,99 | 0,99 | 0,98 | 0,98 | 0,98 |
| Energia real (W) | 510 | 508 | 506 | 514 | 501 |
| Dissipação térmica (BTU/h) | 1740 | 1735 | 1725 | 1753 | 1709 |

## <a name="environmental-requirements"></a>Requisitos ambientais

Esta seção fornece especificações para o ambiente ambiental do hardware.

### <a name="temperature-and-humidity"></a>Temperatura e umidade

| Atributo de ambiente   | Intervalo de operação                   | Intervalo não operacional         |
|---------------------------|-----------------------------------|-----------------------------|
| Intervalo de temperatura ambiente | 10 ° c a 35 ° c (50-86 ° f)          | -40 ° c a 65 ° c (-40-149 ° f) |
| Umidade relativa ao ambiente | 10%-80% sem condensação          | 5%-95% sem condensação     |
| Ponto de orvalho máximo         | 29 ° C (84 ° F)                       | 33 ° C (91 ° F)                 |
| Estabiliza                  | até 3048 metros (10.000 pés), sujeito à reavaliação de temperatura observada abaixo | até 12.000 metros (39.370 pés) |

> [!NOTE] 
> **Desclassificação de temperatura de altitude:** A temperatura máxima é reduzida em 1 ° c/300 m (1 ° f/547 pés) acima de 950 m (3.117 pés).

### <a name="airflow-shock-and-vibration"></a>Fluxo de ar, choque e vibração 

| Atributo         | Specification |
|-------------------|---------------|
| Ar                    | O fluxo de ar do sistema é de frente para trás. O sistema deve ser operado com uma instalação de exaustão traseira de baixa pressão. |
| Choque, operacional         | 6 G por 11 milissegundos (testado em 6 orientações) |
| Choque, não operacional     | 71 G por 2 milissegundos (testado em 6 orientações) |
| Vibração, operacional     | 0,26 G<sub>RMS</sub> 5 Hz a 350 Hz aleatório         |
| Vibração, não operacional | 1,88 G<sub>RMS</sub> 10 hz a 500 Hz por 15 minutos (todos os seis lados testados)  |

## <a name="safety-regulation-compliance"></a>Conformidade com normas de segurança 

O Azure FXT Edge Filer está em conformidade com as regulamentações listadas. 

| Categoria       | Especificação regulatória | 
|----------------|--------------------------|
| Segurança geral | EN 60950-1:2006 + a1:2010 + a2:2013 + a11:2009 + A12:2011/IEC 60950-1:2005 ed2 + a1:2009 + a2:2013 <br>EN 62311:2008 | 
| DA            | FCC A, ICES-003  <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 + A1:2015/CISPR 24:2010 + A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (classe D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013 |
| Energia         | Não há nenhum. 617/2013  |
| RoHS           |    EN 50581:2012   |