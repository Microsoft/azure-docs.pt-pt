---
title: Especificações técnicas StorSimple [ StorSimple] Microsoft Docs
description: Descreve as especificações técnicas e as normas regulamentares de conformidade com os componentes de hardware StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: 061194422a8c1bc449dbef0c4f04bb8e1db10dea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68965294"
---
# <a name="technical-specifications-and-compliance-for-the-storsimple-device"></a>Especificações técnicas e conformidade com o dispositivo StorSimple

## <a name="overview"></a>Descrição geral

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

Os componentes de hardware do seu dispositivo Microsoft Azure StorSimple aderem às especificações técnicas e normas regulamentares descritas neste artigo. As especificações técnicas descrevem os Módulos de Potência e Arrefecimento (PCMs), unidades de disco, capacidade de armazenamento e recintos. As informações de conformidade abrangem coisas como normas internacionais, segurança e emissões e cablagem.

## <a name="power-and-cooling-module-specifications"></a>Especificações do módulo de energia e arrefecimento

O dispositivo StorSimple tem duas ventoinhas duplas de 100-240 V, módulos de arrefecimento de potência compatíveis com SBB (PCMs). Isto fornece uma configuração de potência redundante. Se um PCM falhar, o dispositivo continua a funcionar normalmente no outro PCM até que o módulo falhado seja substituído.

O recinto EBOD utiliza um PCM de 580 W, e o recinto primário utiliza um PCM de 764 W. As tabelas seguintes listam as especificações técnicas associadas aos PCMs.

| Especificação | 580 W PCM (EBOD) | 764 W PCM (Primário) |
| --- | --- | --- |
| Potência máxima de saída |580 W |764 |
| Frequência |50/60 Hz |50/60 Hz |
| Seleção de tensão |Auto ranging: 90 - 264 V AC, 47/63 Hz |Auto ranging: 90-264 V AC, 47/63 Hz |
| Corrente máxima de incorrer |20 A |20 A |
| Correção do fator de potência |>95% de tensão nominal de entrada |>95% de tensão nominal de entrada |
| Harmónicas |Encontros EN61000-3-2 |Encontros EN61000-3-2 |
| Saída |5V Tensão \@ de espera 2.0 A |5V tensão \@ de espera 2.7 A |
| +5V \@ 42 A |+5V \@ 40 A | |
| +12V \@ 38 A |+12V \@ 38 A | |
| Pluggable quente |Sim |Sim |
| Interruptores e LEDs |Interruptor AC ON/OFF e quatro LED indicador de estado |Interruptor AC ON/OFF e seis LED indicador de estado |
| Arrefecimento do recinto |Ventiladores de arrefecimento axial com controlo de velocidade de ventilador variável |Ventiladores de arrefecimento axial com controlo de velocidade de ventilador variável |

## <a name="power-consumption-statistics"></a>Estatísticas do consumo de energia

A tabela que se segue lista os dados típicos de consumo de energia (os valores reais podem variar dos publicados) para os vários modelos do dispositivo StorSimple.

| Condições | 240 V AC | 240 V AC | 240 V AC | 110 V AC | 110 V AC | 110 V AC |
| --- | --- | --- | --- | --- | --- | --- |
|  Os fãs abrandam, conduzem ocioso |1.45 A |0,31 kW |1057.76 BTU/hr |3.19 A |0,34 kW |1160.13 BTU/hr |
|  Fãs lentos, conduz acesso |1.54 A |0,33 kW |1126.01 BTU/hr |3.27 A |0,36 kW |1228.37 BTU/hr |
|  Fãs rápidos, drives ociosos, duas PSUs powered |2.14 A |0,49 kW |1671.95 BTU/hr |4.99 A |0,54 kW |1842.56 BTU/hr |
|  Fãs rápidos, conduz o marcha lenta, uma PSU alimentada um ocioso |2.05 A |0,48 kW |1637.83 BTU/hr |4.58 A |0,50 kW |1706.07 BTU/hr |
|  Fãs rápidos, impulsiona o acesso, duas PSUs powered |2.26 A |0,51 kW |1740.19 BTU/hr |4.95 A |0,54 kW |1842.56 BTU/hr |
|  Fãs rápidos, conduz acesso, uma PSU alimentada um ocioso |2.14 A |0,49 kW |1671.95 BTU/hr |4.81 A |0,53 kW |1808.44 BTU/hr |

## <a name="disk-drive-specifications"></a>Especificações de unidade de disco

O seu dispositivo StorSimple suporta até 12 unidades de disco Serial Attached SCSI (SAS) de até 12 3,5 polegadas. As unidades reais podem ser uma mistura de unidades de estado sólido (SSDs) ou discos rígidos (HDDs), dependendo da configuração do produto. As 12 ranhuras de acionamento do disco estão localizadas numa configuração 3 por 4 em frente ao recinto. O recinto EBOD permite um armazenamento adicional para mais 12 unidades de disco. Estes são sempre HDDs.

## <a name="storage-specifications"></a>Especificações de armazenamento

Os dispositivos StorSimple têm uma mistura de discos rígidos e unidades de estado sólido para os 8100 e 8600. A capacidade total utilizável para os 8100 e 8600 são aproximadamente 15 TB e 38 TB, respectivamente. A tabela seguinte documenta os detalhes de SSD, HDD e capacidade de nuvem no contexto da capacidade de solução StorSimple.

| Modelo de dispositivo / Capacidade | 8100 | 8600 |
| --- | --- | --- |
| Número de discos rígidos (HDDs) |8 |19 |
| Número de unidades de estado sólido (SSDs) |4 |5 |
| Capacidade HDD única |4 TB |4 TB |
| Capacidade única de SSD |400 GB |800 GB |
| Capacidade de reposição |4 TB |4 TB |
| Capacidade utilizável de HDD |14 TB |36 TB |
| Capacidade utilizável de SSD |800 GB |2 TB |
| Total da capacidade utilizável* |~ 15 TB |~ 38 TB |
| Capacidade máxima de solução (incluindo nuvem) |200 TB |500 TB |

<sup>* </sup>- *A capacidade total utilizável inclui a capacidade disponível para dados, metadados e tampão. Pode fornecer volumes fixados localmente até 8,5 TB no dispositivo 8100 ou até 22,5 TB no dispositivo 8600 maior. Para mais informações, vá ao [StorSimple volumes fixados localmente.](storsimple-8000-local-volume-faq.md)*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Dimensões do recinto e especificações de peso

As tabelas seguintes listam as várias especificações do recinto para dimensões e peso.

### <a name="enclosure-dimensions"></a>Dimensões do recinto

A tabela seguinte enumera as dimensões do recinto em milímetros e polegadas.

| Recinto | Milímetros | Centímetros |
| --- | --- | --- |
| Altura |87.9 |3.46 |
| Largura através da flange de montagem |483 |19.02 |
| Largura através do corpo do recinto |443 |17.44 |
| Profundidade da flange de montagem frontal à extremidade do corpo do recinto |577 |22.72 |
| Profundidade do painel de operações para a extremidade mais distante do recinto |630.5 |24.82 |
| Profundidade da montagem da flange até à extremidade mais distante do recinto |603 |23.74 |

### <a name="enclosure-weight"></a>Peso do recinto

Dependendo da configuração, um recinto primário totalmente carregado pode pesar de 21 a 33 kge requer duas pessoas para manuseá-lo.

| Recinto | Peso |
| --- | --- |
| Peso máximo (depende da configuração) |30 kg - 33 kg |
| Vazio (sem unidades montadas) |21 - 23 kg |

## <a name="enclosure-environment-specifications"></a>Especificações ambientais de recinto

Esta secção enumera as especificações relativas ao ambiente do recinto. A temperatura, humidade, altitude, choque, vibração, orientação, segurança e compatibilidade eletromagnética (EMC) estão incluídos nesta categoria.

### <a name="temperature-and-humidity"></a>Temperatura e humidade

| Recinto | Intervalo de temperatura ambiente | Humidade relativa ambiente | Lâmpada molhada máxima |
| --- | --- | --- | --- |
| Operacional |5°C - 35°C (41°F - 95°F) |20% - 80% não condensando- |28°C (82°F) |
| Não operacional |-40°C - 70°C (40°F - 158°F) |5% - 100% não condensado |29°C (84°F) |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Fluxo de ar, altitude, choque, vibração, orientação, segurança e EMC

| Recinto | Especificações operacionais |
| --- | --- |
| Fluxo de ar |O fluxo de ar do sistema é frontal para trás. O sistema deve ser acionado com uma instalação de escape traseiro de baixa pressão. A pressão traseira criada por portas e obstáculos não deve exceder 5 pascals (calibre de água de 0,5 mm). |
| Altitude, operacional |-30 metros a 3045 metros (-100 pés a 10.000 pés) com temperatura máxima de funcionamento desclassificada em 5°C acima de 7.000 pés. |
| Altitude, não operacional |-305 metros a 12.192 metros (-1.000 pés a 40.000 pés) |
| Choque, operacional |5g 10 ms 1/2 sine |
| Choque, não operacional |30g 10 ms 1/2 sine |
| Vibração, operacional |0.21g RMS 5-500 Hz aleatório |
| Vibração, não operacional |1.04g RMS 2-200 Hz aleatório |
| Vibração, deslocalização |3g 2-200 Hz sine |
| Orientação e montagem |Montagem de cremalheira de 19" (2 unidades EIA) |
| Trilhos de cremalheira |Para encaixar no mínimo 700 mm (31,50 polegadas) cremalheiras de profundidade em conformidade com o IEC 297 |
| Segurança e aprovações |CE e UL EN 61000-3, IEC 61000-3, UL 61000-3 |
| EMC |EN55022 (CISPR - A), FCC A |

## <a name="international-standards-compliance"></a>Conformidade das normas internacionais

O seu dispositivo Microsoft Azure StorSimple cumpre os seguintes padrões internacionais:  

* CE - EN 60950 - 1
* Relatório CB ao IEC 60950 - 1
* UL e CUL para UL 60950 - 1

## <a name="safety-compliance"></a>Conformidade de segurança

O seu dispositivo Microsoft Azure StorSimple cumpre as seguintes classificações de segurança:

* Homologação do tipo de produto do sistema: UL, cUL, CE
* Conformidade com a segurança: UL 60950, IEC 60950, EN 60950

## <a name="emc-compliance"></a>Conformidade com a EMC

O seu dispositivo Microsoft Azure StorSimple cumpre as seguintes classificações da EMC.

### <a name="emissions"></a>Emissões

O dispositivo é compatível com a EMC para níveis de emissões conduzidos e irradiados.

* Níveis limite de emissões conduzidos: CFR 47 Parte 15B Classe A EN55022 Classe A CISPR Classe A
* Níveis limite de emissões irradiados: CFR 47 Parte 15B Classe A EN55022 Classe A CISPR Classe A

### <a name="harmonics-and-flicker"></a>Harmónicas e cintilar

O dispositivo está em conformidade com a EN61000-3-2/3.

### <a name="immunity-limit-levels"></a>Níveis limite de imunidade

O dispositivo está em conformidade com a EN55024.

## <a name="ac-power-cord-compliance"></a>Conformidade do cabo de alimentação CA

A ficha e o conjunto completo do cabo de alimentação devem satisfazer as normas adequadas para o país/região em que o dispositivo está a ser utilizado, devendo dispor de aprovações de segurança aceitáveis nesse país/região. As seguintes tabelas listam normas para os EUA e europa.

### <a name="ac-power-cords---usa-must-be-nrtl-listed"></a>Cabos de alimentação CA - EUA (devem estar listados nRTL)

| Componente | Especificação |
| --- | --- |
| Tipo de cabo |SV ou SVT, 18 AWG mínimo, 3 condutores, 2,0 metros de comprimento máximo |
| Ficha |Ficha de ligação à ligação à terra tipo NEMA 5-15P com classificação de 120 V, 10 A; ou IEC 320 C14, 250 V, 10 A |
| Tomada |IEC 320 C-13, 250 V, 10 A |

### <a name="ac-power-cords---europe"></a>Cabos de alimentação AC - Europa

| Componente | Especificação |
| --- | --- |
| Tipo de cabo |Harmonizado, H05-VVF-3G1.0 |
| Tomada |IEC 320 C-13, 250 V, 10 A |

## <a name="supported-network-cables"></a>Cabos de rede suportados

Para as interfaces de rede de 10 GbE, DATA 2 e DATA 3, consulte a [lista de cabos e módulos](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)de rede suportados .

## <a name="next-steps"></a>Passos seguintes

Está agora pronto para implementar um dispositivo StorSimple no seu datacenter. Para mais informações, consulte [a implementação do seu dispositivo no local](storsimple-8000-deployment-walkthrough-u2.md).

