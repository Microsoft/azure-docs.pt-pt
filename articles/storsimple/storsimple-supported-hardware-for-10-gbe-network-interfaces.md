---
title: Hardware para interfaces StorSimple 10 GbE [ StorSimple 10 GbE] Microsoft Docs
description: Descreve os transceptores, cabos e interruptores suportados para as interfaces de rede de 10 GbE suportados no seu dispositivo StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: df8d40c7-f5ad-4f84-93eb-779fbd5f7243
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/21/2016
ms.author: alkohli
ms.openlocfilehash: 7fafe177ea0c6c618dc4ab0727ba14c83cbb0102
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "68965020"
---
# <a name="supported-hardware-for-the-10-gbe-network-interfaces-on-your-storsimple-device"></a>Hardware suportado para as interfaces de rede de 10 GbE no seu dispositivo StorSimple

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Descrição geral
Este artigo fornece informações sobre hardware suplementar que funciona com o seu dispositivo Microsoft Azure StorSimple.

## <a name="list-of-devices-tested-by-microsoft"></a>Lista de dispositivos testados pela Microsoft
A Microsoft testou os seguintes transceptores, cabos e interruptores de pequeno fator de forma (SFP) para garantir que funcionam da melhor forma com os dispositivos. (As tabelas seguintes serão atualizadas à medida que for testado novo hardware.)

### <a name="sfp-transceivers"></a>Transceptores SFP+
| Fabrico | Modelo |
| --- | --- |
| Cisco |SFP-10G-SR |

### <a name="cables"></a>Cabos
| S. Não. | Fabrico | Modelo |
| --- | --- | --- |
| 1. |Cisco |SFP-H10GB-CU1M |
| 2. |Cisco |SFP-H10GB-CU2M |
| 3. |Cisco |SFP-H10GB-CU3M |
| 4. |Tripp-Lite |N820-05M (OM3) |

### <a name="switches"></a>Comutadores
| S. Não. | Fabrico | Modelo |
| --- | --- | --- |
| 1. |Cisco |N3K-C3172PQ-10GE |
| 2. |Cisco |N3K-C3048-ZM-F |
| 3. |Cisco |N5K-C5596UP-FA |

## <a name="list-of-devices-tested-in-the-field"></a>Lista de dispositivos testados no campo
Esta secção contém a lista de dispositivos que foram implementados com sucesso no campo pelos clientes StorSimple. Estes não foram testados pela Microsoft, mas são suscetíveis de funcionar com o seu dispositivo StorSimple.

| Parâmetro | Valor |
| --- | --- |
| Switch fazer |Juniper |
| Modelo de comutação |ex4550-32F |
| Alternar a versão do sistema operativo |JunOS 12.3R9.4 |
| Modelo de lâmina |Portas a bordo (PIC 0) |
| Transceiver fazer |Juniper |
| Modelo transceiver |Número da peça 740-021308 <br></br> Número da peça 740-030658 |
| Versão de firmware Transceiver |Rev 01 Versão 0.0 (reportada) |
| Modelo de cabo |Ponte Duplex LC/LC 50/125μ, OM3, LSZH |
| Modelo StorSimple |8600 |
| Versão de software StorSimple |6.3.9600.17491 |

## <a name="list-of-devices-tested-by-oem-provider-mellanox"></a>Lista de dispositivos testados pelo fornecedor OEM (Mellanox)
A Mellanox testou os seguintes transceptores, cabos e interruptores de pequeno fator de forma (SFP) para garantir que funcionam da melhor forma com interfaces de rede Mellanox, como as interfaces de rede de 10 GbE no seu dispositivo StorSimple.

### <a name="cables-and-modules-supported-by-mellanox"></a>Cabos e módulos suportados pela Mellanox
A tabela seguinte lista os cabos e módulos suportados pela Mellanox. Estes não foram testados pela Microsoft, mas são suscetíveis de funcionar com o seu dispositivo StorSimple.

| S. Não. | Velocidade | Modelo | Descrição | Fabrico |
| --- | --- | --- | --- | --- |
| 1. |10 Gbe |CAB-SFP-SFP-1M |cabo de cobre passivo SFP+ 10 Gb/s 1m |Arista |
| 2. |10 Gbe |CAB-SFP-SFP-2M |cabo de cobre passivo SFP+ 10 Gb/s 2m |Arista |
| 3. |10 Gbe |CAB-SFP-SFP-3M |cabo de cobre passivo SFP+ 10 Gb/s 3m |Arista |
| 4. |10 Gbe |CAB-SFP-SFP-5M |cabo de cobre passivo SFP+ 10 Gb/s 5m |Arista |
| 5. |10 Gbe |Cisco SFP-H10GBCU1M |Cabo Cisco SFP+ |Cisco |
| 6. |10 Gbe |Cisco SFP-H10GBCU3M |Cabo Cisco SFP+ |Cisco |
| 7. |10 Gbe |Cisco SFP-H10GBCU5M |Cabo Cisco SFP+ |Cisco |
| 8. |10 Gbe |J9281B HP X242 10G |SFP+ para SFP+ 1m Cabo de cobre de fixação direta |HP |
| 9. |10 Gbe |455883-B21 HP BLc |10GB SR SFP+ Opt |HP |
| 10. |10 Gbe |455886-B21 HP BLc |10gb LR SFP+ Opt |HP |
| 11. |10 Gbe |487649-B21 HP BLc |Cabo de cobre SFP+ 0,5m 10GbE |HP |
| 12. |10 Gbe |487652-B21 HP BLc |Cabo de cobre SFP+ 1m 10GbE |HP |
| 13. |10 Gbe |487655-B21 HP BLc |Cabo de cobre SFP+ 3m 10GbE |HP |
| 14. |10 Gbe |487658-B21 HP BLc |Cabo de cobre SFP+ 7m 10GbE |HP |
| 15. |10 Gbe |537963-B21 HP BLc |Cabo de cobre SFP+ 5m 10GbE |HP |
| 16. |10 Gbe |AP784A HP |Cabo 3m C-série De Cobre Passivo SFP+ |HP |
| 17. |10 Gbe |AP785A HP |Cabo SFP+ de cobre passivo de 5m c |HP |
| 18. |10 Gbe |AP818A HP |Cabo 1m B-série De Cobre Ativo SFP+ |HP |
| 19. |10 Gbe |AP819A HP |Cabo 3m B-série De Cobre Ativo SFP+ |HP |
| 20. |10 Gbe |J9150A HP |X132 10G SFP+ LC SR Transceiver |HP |
| 21. |10 Gbe |J9151A HP |X132 10G SFP+ LC LR Transceiver |HP |
| 22. |10 Gbe |J9283B HP |X242 10G SFP+ SFP+ 3m DAC Cabo |HP |
| 23. |10 Gbe |J9285B HP |X242 10G SFP+ SFP+ 7m CABO DAC |HP |
| 24. |10 Gbe |JD095B HP |X240 10G SFP+ SFP+ 0,65m CABO DAC |HP |
| 25. |10 Gbe |JD096B HP |X240 10G SFP+ SFP+ Cabo DAC 1.2m |HP |
| 26. |10 Gbe |JD097B HP |X240 10G SFP+ SFP+ 3m DAD Cabo |HP |
| 27. |10 Gbe |MAM1Q00A-QSA Mellanox |Adaptador QSFP Para SFP+ |Tecnologias Mellanox |
| 28. |10 Gbe |MC2309124-006 Mt |Cabo de cobre passivo 1x SFP+ A QSFP 10Gb/s 24awg 7m |Tecnologias Mellanox |
| 29. |10 Gbe |MC2309124-007 Mt |Cabo de cobre passivo 1x SFP+ A QSFP 10Gb/s 24awg 7m |Tecnologias Mellanox |
| 30. |10 Gbe |MC2309130-003 Mt |Cabo de cobre passivo 1x SFP+ A QSFP 10Gb/s 30awg 3m |Tecnologias Mellanox |
| 31. |10 Gbe |MC2309130-00A Mt |Cabo de cobre passivo 1x SFP+ A QSFP 10Gb/s 30awg 0.5m |Tecnologias Mellanox |
| 32. |10 Gbe |MC3309124-005 Mt |Cabo de Cobre Passivo 1x SFP+ 10Gb/s 24awg 5m |Tecnologias Mellanox |
| 33. |10 Gbe |MC3309124-007 Mt |Cabo de Cobre Passivo 1x SFP+ 10Gb/s 24awg 7m |Tecnologias Mellanox |
| 34. |10 Gbe |MC3309130-003 Mt |Cabo de Cobre Passivo 1x SFP+ 10Gb/s 30awg 3m |Tecnologias Mellanox |
| 35. |10 Gbe |MC3309130-00A Mt |Cabo de Cobre Passivo 1x SFP+ 10Gb/s 30awg 0.5m |Tecnologias Mellanox |

### <a name="switches-supported-by-mellanox"></a>Switches suportados por Mellanox
A tabela seguinte lista os interruptores suportados por Mellanox. Estes não foram testados pela Microsoft, mas são suscetíveis de funcionar com o seu dispositivo StorSimple.

| S. Não. | Velocidade | Modelo | Descrição | Fabrico |
| --- | --- | --- | --- | --- |
| 1. |10Gbe |516733-B21 |HP ProCurve 6120XG 10GbE Ethernet Blade Switch |HP |
| 2. |10Gbe |538113-B21 |Módulo de passagem HP 10Gbe (PTM) |HP |
| 3. |10Gbe |EN4093 |IBM PureFlex System Fabric EN4093 10 Gigabit Scalable Switch Módulo |IBM |
| 4. |1Gbe |3020 |Cisco Catalyst 3020 1Gbe switch blade |Cisco |
| 5. |1Gbe |3020X |Cisco Catalyst 3020X 1GbE switch blade |Cisco |
| 6. |1Gbe |438030-B21 |Módulo de comutação HP 1GbE - Camada GbE2c 2/3 Interruptor de Lâmina Ethernet |HP |
| 7. |1Gbe |6120G |Hp ProCurve 6120G/XG 1GbE comuta |HP |

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre os componentes de hardware StorSimple e o estado](storsimple-monitor-hardware-status.md).

