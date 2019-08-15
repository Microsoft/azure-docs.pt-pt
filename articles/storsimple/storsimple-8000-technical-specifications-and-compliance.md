---
title: Especificações técnicas do StorSimple | Microsoft Docs
description: Descreve as especificações técnicas e as informações de conformidade dos padrões regulatórios para os componentes de hardware do StorSimple.
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
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68965294"
---
# <a name="technical-specifications-and-compliance-for-the-storsimple-device"></a>Especificações técnicas e conformidade para o dispositivo StorSimple

## <a name="overview"></a>Descrição geral

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

Os componentes de hardware de seu dispositivo Microsoft Azure StorSimple aderem às especificações técnicas e aos padrões regulatórios descritos neste artigo. As especificações técnicas descrevem os PCMs (módulos de energia e resfriamento), as unidades de disco, a capacidade de armazenamento e os compartimentos. As informações de conformidade abrangem coisas como padrões internacionais, segurança e emissões e cabeamento.

## <a name="power-and-cooling-module-specifications"></a>Especificações do módulo de energia e resfriamento

O dispositivo StorSimple tem dois PCMs (módulos de resfriamento de energia) de 100-240 V com ventilador duplo, em conformidade com SBB. Isso fornece uma configuração de energia redundante. Se um PCM falhar, o dispositivo continuará a operar normalmente no outro PCM até que o módulo com falha seja substituído.

O compartimento EBOD usa um PCM de 580 W e o compartimento primário usa um PCM de 764 W. As tabelas a seguir listam as especificações técnicas associadas ao PCMs.

| Specification | PCM DE 580 W (EBOD) | PCM de 764 W (primário) |
| --- | --- | --- |
| Potência de saída máxima |580 W |764 |
| Frequência |50/60 Hz |50/60 Hz |
| Seleção de faixa de tensão |Variação automática: 90 – 264 V AC, 47/63 Hz |Variação automática: 90- 264 V AC, 47/63 Hz |
| Corrente máxima de incorrida |20 A |20 A |
| Correção de fator de energia |tensão de entrada nominal de > 95% |tensão de entrada nominal de > 95% |
| Harmônicas |Atende a EN61000-3-2 |Atende a EN61000-3-2 |
| Output |tensão \@ de espera de 5V 2,0 A |tensão \@ de espera de 5V 2,7 A |
| +5V \@ 42 A |+5V \@ 40 A | |
| \+ 12V \@ 38 A |\+ 12V \@ 38 A | |
| Conectável a quente |Sim |Sim |
| Interruptores e LEDs |Interruptor de ligar/desligar AC e quatro LEDs indicadores de status |Interruptor de ligar/desligar AC e seis LEDs indicadores de status |
| Resfriamento de compartimento |Ventiladores de resfriamento axial com controle de velocidade de ventilador variável |Ventiladores de resfriamento axial com controle de velocidade de ventilador variável |

## <a name="power-consumption-statistics"></a>Estatísticas de consumo de energia

A tabela a seguir lista os dados de consumo de energia típicos (os valores reais podem variar do publicado) para os vários modelos do dispositivo StorSimple.

| Condições | 240 V AC | 240 V AC | 240 V AC | 110 V AC | 110 V AC | 110 V AC |
| --- | --- | --- | --- | --- | --- | --- |
|  Ventiladores lentos, unidades ociosas |1,45 A |0,31 kW |1057,76 BTU/h |3,19 A |0,34 kW |1160,13 BTU/h |
|  Ventiladores lentos, unidades acessando |1,54 A |0,33 kW |1126, 1 BTU/h |3,27 A |0,36 kW |1228,37 BTU/h |
|  Ventiladores rápidos, unidades ociosas, duas PSUs |2,14 A |0,49 kW |1671,95 BTU/h |4,99 A |0,54 kW |1842,56 BTU/h |
|  Ventiladores rápidos, unidades ociosas, um PSU ligado um ocioso |2, 5 A |0,48 kW |1637,83 BTU/h |4,58 A |0,50 kW |1706, 7 BTU/h |
|  Ventiladores rápidos, unidades acessando, duas PSUs |2,26 A |0,51 kW |1740,19 BTU/h |4,95 A |0,54 kW |1842,56 BTU/h |
|  Ventiladores rápidos, unidades acessando, um PSU ligado um ocioso |2,14 A |0,49 kW |1671,95 BTU/h |4,81 A |0,53 kW |1808,44 BTU/h |

## <a name="disk-drive-specifications"></a>Especificações de unidade de disco

Seu dispositivo StorSimple dá suporte a até 12 unidades de disco SAS (SCSI anexado em série) de fator forma de 3,5 polegadas. As unidades reais podem ser uma mistura de unidades de estado sólido (SSDs) ou unidades de disco rígido (HDDs), dependendo da configuração do produto. Os 12 slots de unidade de disco estão localizados em uma configuração de 3 a 4 na frente do compartimento. O compartimento EBOD permite armazenamento adicional para outras 12 unidades de disco. Esses são sempre HDDs.

## <a name="storage-specifications"></a>Especificações de armazenamento

Os dispositivos StorSimple têm uma mistura de unidades de disco rígido e unidades de estado sólido para 8100 e 8600. A capacidade utilizável total para 8100 e 8600 tem aproximadamente 15 TB e 38 TB, respectivamente. A tabela a seguir documenta os detalhes de SSD, HDD e capacidade de nuvem no contexto da capacidade da solução StorSimple.

| Capacidade/modelo do dispositivo | 8100 | 8600 |
| --- | --- | --- |
| Número de unidades de disco rígido (HDDs) |8 |19 |
| Número de unidades de estado sólido (SSDs) |4 |5 |
| Capacidade de HDD único |4 TB |4 TB |
| Capacidade SSD única |400 GB |800 GB |
| Capacidade de reposição |4 TB |4 TB |
| Capacidade de HDD utilizável |14 TB |36 TB |
| Capacidade de SSD utilizável |800 GB |2 TB |
| Capacidade utilizável total * |~ 15 TB |~ 38 TB |
| Capacidade máxima da solução (incluindo nuvem) |200 TB |500 TB |

<sup>* </sup>- *A capacidade utilizável total inclui a capacidade disponível para dados, metadados e buffers. Você pode provisionar volumes localmente afixados de até 8,5 TB no dispositivo 8100 ou até 22,5 TB no dispositivo 8600 maior. Para obter mais informações, vá para [volumes afixados localmente do StorSimple](storsimple-8000-local-volume-faq.md).*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Dimensões de compartimento e especificações de peso

As tabelas a seguir listam as várias especificações de compartimento para dimensões e peso.

### <a name="enclosure-dimensions"></a>Dimensões do compartimento

A tabela a seguir lista as dimensões do compartimento em milímetros e polegadas.

| SPE | Milímetros | 5,08 |
| --- | --- | --- |
| Altura |87.9 |3.46 |
| Largura entre flange de montagem |483 |19.02 |
| Largura no corpo do compartimento |443 |17.44 |
| Profundidade do flange de montagem frontal até o distante do corpo do compartimento |577 |22.72 |
| Profundidade do painel de operações para distante mais distante do compartimento |630.5 |24.82 |
| Profundidade da montagem de flange para distante mais distante do compartimento |603 |23.74 |

### <a name="enclosure-weight"></a>Peso do compartimento

Dependendo da configuração, um compartimento primário totalmente carregado pode pesar de 21 a 33 kg e requer duas pessoas para tratá-lo.

| SPE | Peso |
| --- | --- |
| Peso máximo (depende da configuração) |30 kg – 33 kg |
| Vazio (nenhuma unidade ajustada) |21 – 23 kg |

## <a name="enclosure-environment-specifications"></a>Especificações de ambiente de compartimento

Esta seção lista as especificações relacionadas ao ambiente de compartimento. A temperatura, a umidade, a altitude, o choque, a vibração, a orientação, a segurança e a compatibilidade eletromagnética (EMC) estão incluídas nessa categoria.

### <a name="temperature-and-humidity"></a>Temperatura e umidade

| SPE | Intervalo de temperatura ambiente | Umidade relativa ao ambiente | Lâmpada úmida máxima |
| --- | --- | --- | --- |
| Operacional |5 ° C-35 ° C (41 ° F-95 ° F) |20% a 80% sem condensação- |28°C (82°F) |
| Não operacional |-40 ° C-70 ° C (40 ° F-158 ° F) |5%-100% sem condensação |29 ° C (84 ° F) |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Fluxo de ar, altitude, choque, vibração, orientação, segurança e EMC

| SPE | Especificações operacionais |
| --- | --- |
| Ar |O fluxo de ar do sistema é de frente para trás. O sistema deve ser operado com uma instalação de exaustão traseira de baixa pressão. A pressão traseira criada por portas de rack e obstáculos não deve exceder 5 pascals (medidor de água de 0,5 mm). |
| Altitude, operacional |-30 metros a 3045 metros (-100 pés a 10.000 pés) com a temperatura operacional máxima desclassificada por 5 ° c acima de 7000 pés. |
| Altitude, não operacional |-305 metros a 12.192 metros (-1.000 pés a 40.000 pés) |
| Choque, operacional |5g 10 MS 1/2 seno |
| Choque, não operacional |30g 10 MS 1/2 seno |
| Vibração, operacional |0.21 g RMS 5-500 Hz aleatório |
| Vibração, não operacional |1.04 g RMS 2-200 Hz aleatório |
| Vibração, realocação |seno de 2-200 Hz 3G |
| Orientação e montagem |"montagem em rack" de 19 (2 unidades EIA) |
| Trilhos do rack |Para ajustar os racks de profundidade no mínimo 700 mm (31,50 polegadas) em conformidade com o IEC 297 |
| Segurança e aprovações |CE e UL EN 61000-3, IEC 61000-3, UL 61000-3 |
| DA |EN55022 (CISPR-A), FCC A |

## <a name="international-standards-compliance"></a>Conformidade com os padrões internacionais

Seu dispositivo Microsoft Azure StorSimple está em conformidade com os seguintes padrões internacionais:  

* CE-EN 60950-1
* Relatório CB para IEC 60950-1
* UL e cUL para UL 60950-1

## <a name="safety-compliance"></a>Conformidade de segurança

O dispositivo Microsoft Azure StorSimple atende às seguintes classificações de segurança:

* Aprovação do tipo de produto do sistema: UL, cUL, CE
* Conformidade de segurança: UL 60950, IEC 60950, EN 60950

## <a name="emc-compliance"></a>Conformidade da EMC

Seu dispositivo Microsoft Azure StorSimple atende às classificações da EMC a seguir.

### <a name="emissions"></a>Emissões

O dispositivo é compatível com a EMC para os níveis de emissões realizados e irradiados.

* Níveis de limite de emissões conduzidos: CFR 47 parte 15B classe A EN55022 classe A CISPR classe a
* Níveis de limite de emissões radiadas: CFR 47 parte 15B classe A EN55022 classe A CISPR classe a

### <a name="harmonics-and-flicker"></a>Harmônicas e cintilação

O dispositivo está em conformidade com EN61000-3-2/3.

### <a name="immunity-limit-levels"></a>Níveis de limite de imunidade

O dispositivo está em conformidade com o EN55024.

## <a name="ac-power-cord-compliance"></a>Conformidade do cabo de alimentação AC

O plug e o conjunto completo do cabo de alimentação devem atender aos padrões apropriados para o país/região em que o dispositivo está sendo usado, e eles devem ter aprovações de segurança aceitáveis nesse país/região. As tabelas a seguir listam os padrões para os EUA e Europa.

### <a name="ac-power-cords---usa-must-be-nrtl-listed"></a>Cabos de alimentação CA-EUA (deve ser NRTL listado)

| Componente | Specification |
| --- | --- |
| Tipo de cabo |VA ou SVT, 18 AWG mínimo, 3 condutores, 2,0 medidores de comprimento máximo |
| Tecnologia |Conexão de anexo de tipo de aterramento NEMA 5-15P com classificação 120 V, 10 A; ou IEC 320 C14, 250 V, 10 A |
| SSA |IEC 320 C-13, 250 V, 10 A |

### <a name="ac-power-cords---europe"></a>Cabos de alimentação AC-Europa

| Componente | Specification |
| --- | --- |
| Tipo de cabo |Harmonizado, H05-VVF-3G 1.0 |
| SSA |IEC 320 C-13, 250 V, 10 A |

## <a name="supported-network-cables"></a>Cabos de rede com suporte

Para as interfaces de rede de 10 GbE, DATA 2 e DATA 3, consulte a [lista de módulos e cabos de rede com suporte](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).

## <a name="next-steps"></a>Passos Seguintes

Agora você está pronto para implantar um dispositivo StorSimple em seu datacenter. Para obter mais informações, consulte Implantando [seu dispositivo local](storsimple-8000-deployment-walkthrough-u2.md).

