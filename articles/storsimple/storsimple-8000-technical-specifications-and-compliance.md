---
title: As especificações técnicas do StorSimple | Documentos da Microsoft
description: Descreve as especificações técnicas e informações de conformidade de padrões normativos para os componentes de hardware do StorSimple.
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
ms.openlocfilehash: b36d721896bd7b4f95d831eded500a96969937c5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60631894"
---
# <a name="technical-specifications-and-compliance-for-the-storsimple-device"></a>As especificações técnicas e a conformidade do dispositivo StorSimple

## <a name="overview"></a>Descrição geral

Os componentes de hardware do seu dispositivo do Microsoft Azure StorSimple cumprem as especificações técnicas e os padrões normativos descritos neste artigo. As especificações técnicas descrevem Power e módulos de refrigeração (PCMs), unidades de disco, a capacidade de armazenamento e inclusões. As informações de conformidade abrangerem itens como padrões internacionais, segurança e as emissões e cabeamento.

## <a name="power-and-cooling-module-specifications"></a>Especificações de energia e refrigeração módulo

O dispositivo StorSimple tem dois 100 a 240 V dupla ventoinha, em conformidade SBB Power refrigeração módulos (PCMs). Isso fornece uma configuração de energia redundante. Se um PCM falhar, o dispositivo continua a funcionar normalmente na outro PCM até que o módulo com falha é substituído.

A inclusão EBOD utiliza um PCM W 580 e bastidor principal utiliza um PCM W 764. As tabelas seguintes listam as especificações técnicas associadas as PCMs.

| Especificação | 580 W PCM (EBOD) | 764 W PCM (primário) |
| --- | --- | --- |
| Potência de saída máximo |580 W |764 |
| Frequência |50/60 Hz |50/60 Hz |
| Seleção do intervalo de tensão |Automaticamente, desde: 90 – 264 V AC, 47/63 Hz |Automaticamente, desde: 90- 264 V AC, 47/63 Hz |
| Inrush máximo atual |20 A |20 A |
| Correção de fator de energia |> 95% nominal tensão de entrada |> 95% nominal tensão de entrada |
| Harmonics |Cumpre EN61000-3-2 |Cumpre EN61000-3-2 |
| Saída |Tensão de modo de espera de 5 v \@ 2.0 A |Tensão de modo de espera de 5 v \@ 2.7 A |
| +5V \@ 42 A |+5V \@ 40 A | |
| +12V \@ 38 A |+12V \@ 38 A | |
| Armazenamento frequente conectável |Sim |Sim |
| Comutadores e LEDs |Comutador de ativa/inativa de AC e LED indicadores de estado de quatro |Comutador de ativa/inativa de AC e LED indicadores de estado de seis |
| Bastidor de arrefecimento |Axial refrigeração fãs com controle de velocidade de variável ventoinha |Axial refrigeração fãs com controle de velocidade de variável ventoinha |

## <a name="power-consumption-statistics"></a>Estatísticas de consumo de energia

A tabela seguinte lista os dados de consumo de energia típico (os valores reais podem variar entre as publicado) para os diversos modelos do dispositivo StorSimple.

| Condições | 240 V AC | 240 V AC | 240 V AC | 110 V AC | 110 V AC | 110 V AC |
| --- | --- | --- | --- | --- | --- | --- |
|  Unidades lentas, fãs Inativas |1.45 A |0.31 kW |1057.76 BTU/h |3.19 A |0.34 kW |1160.13 BTU/h |
|  Os fãs mais lenta, unidades de aceder ao |1.54 A |0,33 kW |1126.01 BTU/h |3.27 A |0.36 kW |1228.37 BTU/h |
|  Os fãs rápidas, unidades ociosas, dois PSUs com tecnologia |2.14 A |0,49 kW |1671.95 BTU/h |4.99 A |0.54 kW |1842.56 BTU/h |
|  Fãs rápidos, unidades ocioso, um PSU com tecnologia um inativo |2.05 A |0.48 kW |1637.83 BTU/h |4.58 A |0,50 kW |1706.07 BTU/h |
|  Unidades de velocidade, fãs aceder, dois PSUs com tecnologia |2.26 A |0.51 kW |1740.19 BTU/h |4.95 A |0.54 kW |1842.56 BTU/h |
|  Rapidamente, ventoinhas, unidades de aceder, um PSU com tecnologia um inativo |2.14 A |0,49 kW |1671.95 BTU/h |4.81 A |0.53 kW |1808.44 BTU/h |

## <a name="disk-drive-specifications"></a>Especificações de unidade de disco

O dispositivo StorSimple oferece suporte a até 12 unidades de disco do Serial Attached SCSI (SAS) de fator de formulário 3.5 polegadas. As unidades reais podem ser uma mistura de unidades de estado sólido (SSDs) ou unidades de disco rígido (HDDs), dependendo da configuração do produto. As 12 ranhuras de unidade de disco estão localizadas numa configuração de 3 a 4 na frente o bastidor. Permite a inclusão EBOD para armazenamento adicional para outro 12 unidades de disco. Estas são sempre HDDs.

## <a name="storage-specifications"></a>Especificações de armazenamento

Os dispositivos do StorSimple com uma combinação de unidades de disco rígido e unidades de estado sólido para o 8100 e 8600. A capacidade total utilizável para o 8100 e 8600 são aproximadamente 15 TB e 38 TB respectivamente. A tabela seguinte documenta os detalhes de capacidade de nuvem no contexto da capacidade de solução StorSimple, HDD e SSD.

| Modelo do dispositivo / capacidade | 8100 | 8600 |
| --- | --- | --- |
| Número de unidades de disco rígido (HDDs) |8 |19 |
| Número de unidades de estado sólido (SSDs) |4 |5 |
| Capacidade HDD única |4 TB |4 TB |
| Capacidade SSD única |400 GB |800 GB |
| Capacidade de reserva |4 TB |4 TB |
| Capacidade utilizável de HDD |14 TB |36 TB |
| Capacidade utilizável de SSD |800 GB |2 TB |
| Capacidade total utilizável * |~ 15 TB |~ 38 TB |
| Capacidade máxima de solução (incluindo na nuvem) |200 TB |500 TB |

<sup>* </sup>- *Capacidade utilizável de total inclui a capacidade disponível para dados, metadados e buffers. Pode aprovisionar volumes localmente afixados até 8,5 TB no dispositivo 8100 ou até 22,5 TB no dispositivo 8600 maior. Para obter mais informações, aceda a [volumes do StorSimple localmente afixados](storsimple-8000-local-volume-faq.md).*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Dimensões de bastidor e especificações de peso

As tabelas seguintes listam as várias especificações de inclusão para dimensões e peso.

### <a name="enclosure-dimensions"></a>Dimensões de bastidor

A tabela seguinte lista as dimensões de inclusão em milímetros e polegadas.

| Bastidor | Milímetros | Polegadas |
| --- | --- | --- |
| Altura |87.9 |3.46 |
| Largura em flange de montagem |483 |19.02 |
| Largura em corpo de bastidor |443 |17.44 |
| Questão de flange de montagem de front-extremity do corpo de bastidor |577 |22.72 |
| Profundidade do painel de operações para extremity furthest de bastidor |630.5 |24.82 |
| Profundidade de flange de montagem para extremity furthest de bastidor |603 |23.74 |

### <a name="enclosure-weight"></a>Peso de bastidor

Dependendo da configuração, um bastidor principal totalmente carregado pode ponderar de 21 para 33 kgs e requer duas pessoas manipulá-lo.

| Bastidor | Peso |
| --- | --- |
| Peso máximo (depende da configuração) |30 kg – 33 kg |
| Vazio (não existem unidades equipados) |21 – 23 kg |

## <a name="enclosure-environment-specifications"></a>Especificações de ambiente de bastidor

Esta secção lista as especificações relacionadas com o ambiente de bastidor. A temperatura, umidade, altitude, choque, vibração, orientação, segurança e compatibilidade Eletromagnéticas (EMC) estão incluídos nesta categoria.

### <a name="temperature-and-humidity"></a>Temperatura e humidade

| Bastidor | Intervalo de temperatura ambiente | Ambiente de caminho relativo humidade | Bolbo wet máximo |
| --- | --- | --- | --- |
| Operacional |5°C - 35°C(41°F - 95°F) |20% a 80% - não-condensar- |28°C (82°F) |
| Não-operacional |-40°C - 70°C(40°F - 158°F) |5% a 100% - não-condensar |29°C (84°F) |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Ventilação, altitude, choque, vibração, orientação, segurança e EMC

| Bastidor | Especificações operacionais |
| --- | --- |
| Ventilação |Ventilação do sistema é, de frente para trás. Sistema tem de ser utilizado com uma instalação low-pressure esgotar para trás. Ter pressão contrária proveniente criado por rack portas e os obstáculos não deve exceder 5 pascals (medidor de água de 0,5 mm). |
| Altitude, operacional |Medidores de-30 para medidores 3045 (-100 metros para 10 000 metros) com o máxima temperatura operacional anular classificada pelos 5c ° acima pés 7000. |
| Altitude, inoperacional |Medidores de-305 para medidores 12,192 (-1,000 pés para 40.000 metros) |
| Choque, operacional |seno de 10 ms ½ 5g |
| Choque, inoperacional |seno de 10 ms ½ 30g |
| Vibração, operacional |0.21g RMS 5-500 Hz aleatório |
| Vibração, inoperacional |1.04g RMS Hz de 2 200 aleatório |
| Vibração, reposicionamento |seno de 2 200 Hz 3g |
| Orientação e montagem |19" montar em bastidor (2 unidades EIA) |
| Bastidor rails |De acordo com a profundidade de mm 700 mínimo (31.50 polegadas) racks em conformidade com IEC 297 |
| Segurança e aprovações |CE e UL EN 61000-3, IEC 61000-3, UL 61000-3 |
| EMC |EN55022 (CISPR - A), FCC A |

## <a name="international-standards-compliance"></a>Conformidade com os padrões internacionais

O dispositivo do Microsoft Azure StorSimple está em conformidade com as normas internacionais seguintes:  

* CE - EN 60950-1
* Relatório de CB para IEC 60950-1
* UL e cUL para UL 60950-1

## <a name="safety-compliance"></a>Conformidade de segurança

O dispositivo do Microsoft Azure StorSimple satisfaz as seguintes classificações de segurança:

* Aprovação do tipo de produto de sistema: UL, cUL, CE
* Conformidade de segurança: UL 60950, IEC 60950, EN 60950

## <a name="emc-compliance"></a>Conformidade de EMC

O dispositivo do Microsoft Azure StorSimple satisfaz as seguintes classificações de EMC.

### <a name="emissions"></a>Emissões

O dispositivo está em conformidade com o EMC para níveis de emissões realizadas e radiated.

* Níveis de limite de emissões de realizada: CFR parte 47 15B classe A EN55022 classe A CISPR classe A
* Níveis de limite de emissões de radiated: CFR parte 47 15B classe A EN55022 classe A CISPR classe A

### <a name="harmonics-and-flicker"></a>Harmonics e cintilação

O dispositivo está em conformidade com EN61000-3-2/3.

### <a name="immunity-limit-levels"></a>Níveis de limite de Immunity

O dispositivo está em conformidade com EN55024.

## <a name="ac-power-cord-compliance"></a>Conformidade de cabo de energia de AC

O plug e o assembly de cabo power completa têm de cumprir os padrões apropriados para o país em que o dispositivo está a ser utilizado e têm de ter as aprovações de segurança que são aceitáveis naquele país. As tabelas seguintes listam os padrões para os EUA e Europa.

### <a name="ac-power-cords---usa-must-be-nrtl-listed"></a>Cabos de energia de AC - EUA (tem de ser NRTL listado)

| Componente | Especificação |
| --- | --- |
| Tipo de cabo |Comprimento máximo de medidores 2.0 SV ou SVT, 18 de AWG de mínimo, 3 conductor, |
| Plug |Plug de anexo de tipo de aterrados NEMA P de 5 a 15 com avaliação de 120 V, 10 A; ou IEC 320 C14, 250 V, de 10 A |
| Socket |IEC 320 C-13, 250 V, 10 A |

### <a name="ac-power-cords---europe"></a>Cabos de energia de AC - Europa

| Componente | Especificação |
| --- | --- |
| Tipo de cabo |Harmonized, H05-VVF-3G1.0 |
| Socket |IEC 320 C-13, 250 V, 10 A |

## <a name="supported-network-cables"></a>Cabos de rede suportados

Para interfaces de rede de 10 GbE, dados 2 e 3 de dados, consulte a [lista de módulos e cabos de rede suportados](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).

## <a name="next-steps"></a>Passos Seguintes

Está agora pronto para implementar um dispositivo StorSimple no seu datacenter. Para obter mais informações, consulte [Implantando o seu dispositivo no local](storsimple-8000-deployment-walkthrough-u2.md).

