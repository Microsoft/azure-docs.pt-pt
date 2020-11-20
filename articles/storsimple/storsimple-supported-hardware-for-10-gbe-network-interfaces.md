---
title: Hardware para interfaces StorSimple 10 GbE Microsoft Docs
description: Descreve os pequenos fatores de forma pluggable (SFP) transceptores, cabos e interruptores para as interfaces de rede de 10 GbE no seu dispositivo StorSimple.
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
ms.openlocfilehash: fd30c7ec15eadd3d7945349a8aae4ac824bfdc7f
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94954091"
---
# <a name="supported-hardware-for-the-10-gbe-network-interfaces-on-your-storsimple-device"></a>Hardware suportado para as interfaces de rede de 10 GbE no seu dispositivo StorSimple

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Descrição geral
Este artigo fornece informações sobre hardware suplementar que funciona com o seu dispositivo Microsoft Azure StorSimple.

## <a name="list-of-devices-tested-by-microsoft"></a>Lista de dispositivos testados pela Microsoft
A Microsoft testou os seguintes pequenos transceptores de fator de forma (SFP) e comutadores para garantir que funcionam da melhor forma com os dispositivos. (As seguintes tabelas serão atualizadas à medida que o novo hardware for testado.)

### <a name="sfp-transceivers"></a>SFP+ Transceivers
| Criação | Modelação |
| --- | --- |
| Cisco |SFP-10G-SR |

### <a name="cables"></a>Cabos
| S. Não. | Criação | Modelação |
| --- | --- | --- |
| 1. |Cisco |SFP-H10GB-CU1M |
| 2. |Cisco |SFP-H10GB-CU2M |
| 3. |Cisco |SFP-H10GB-CU3M |
| 4. |Tripp-Lite |N820-05M (OM3) |

### <a name="switches"></a>Comutadores
| S. Não. | Criação | Modelação |
| --- | --- | --- |
| 1. |Cisco |N3K-C3172PQ-10GE |
| 2. |Cisco |N3K-C3048-ZM-F |
| 3. |Cisco |N5K-C5596UP-FA |

## <a name="list-of-devices-tested-in-the-field"></a>Lista de dispositivos testados no campo
Esta secção contém a lista de dispositivos que foram implementados com sucesso no campo por clientes StorSimple. Estes não foram testados pela Microsoft, mas são suscetíveis de funcionar com o seu dispositivo StorSimple.

| Parâmetro | Valor |
| --- | --- |
| Fazer switch |Juniper |
| Modelo de comutação |ex4550-32F |
| Versão do sistema operativo Switch |JunOS 12.3R9.4 |
| Modelo de lâmina |Portas a bordo (PIC 0) |
| Transceiver fazer |Juniper |
| Modelo transceiver |Número de peça 740-021308 <br></br> Número de peça 740-030658 |
| Versão de firmware transceiver |Rev 01 Versão 0.0 (reportado) |
| Modelo de cabo |Duplex jumper LC/LC 50/125μ, OM3, LSZH |
| Modelo StorSimple |8600 |
| Versão de software StorSimple |6.3.9600.17491 |

## <a name="list-of-devices-tested-by-oem-provider-mellanox"></a>Lista de dispositivos testados pelo fornecedor OEM (Mellanox)
A Mellanox testou os seguintes pequenos transceptores pluggable (SFP) de fator de forma, e comutadores para garantir que funcionam da melhor forma com interfaces de rede Mellanox, como as interfaces de rede de 10 GbE no seu dispositivo StorSimple.

### <a name="cables-and-modules-supported-by-mellanox"></a>Cabos e módulos suportados pela Mellanox
A tabela que se segue lista os cabos e módulos suportados pela Mellanox. Estes não foram testados pela Microsoft, mas são suscetíveis de funcionar com o seu dispositivo StorSimple.

| S. Não. | Velocidade | Modelação | Descrição | Criação |
| --- | --- | --- | --- | --- |
| 1. |10 Gbe |CAB-SFP-SFP-1M |cabo de cobre passivo SFP+ 10 Gb/s 1m |Rio Arista |
| 2. |10 Gbe |CAB-SFP-SFP-2M |cabo de cobre passivo SFP+ 10 Gb/s 2m |Rio Arista |
| 3. |10 Gbe |CAB-SFP-SFP-3M |cabo de cobre passivo SFP+ 10 Gb/s 3m |Rio Arista |
| 4. |10 Gbe |CAB-SFP-SFP-5M |cabo de cobre passivo SFP+ 10 Gb/s 5m |Rio Arista |
| 5. |10 Gbe |Cisco SFP-H10GBCU1M |Cabo Cisco SFP+ |Cisco |
| 6. |10 Gbe |Cisco SFP-H10GBCU3M |Cabo Cisco SFP+ |Cisco |
| 7. |10 Gbe |Cisco SFP-H10GBCU5M |Cabo Cisco SFP+ |Cisco |
| 8. |10 Gbe |J9281B HP X242 10G |SFP+ a SFP+ 1m Cabo de Cobre De Ligação Direta |HP |
| 9. |10 Gbe |455883-B21 HP BLc |10Gb SR SFP+ Opt |HP |
| 10. |10 Gbe |455886-B21 HP BLc |10Gb LR SFP+ Opt |HP |
| 11. |10 Gbe |487649-B21 HP BLc |Cabo de cobre SFP+ 0,5m 10GbE |HP |
| 12. |10 Gbe |487652-B21 HP BLc |Cabo de cobre SFP+ 1m 10GbE |HP |
| 13. |10 Gbe |487655-B21 HP BLc |Cabo de cobre SFP+ 3m 10GbE |HP |
| 14. |10 Gbe |487658-B21 HP BLc |Cabo de cobre SFP+ 7m 10GbE |HP |
| 15. |10 Gbe |537963-B21 HP BLc |Cabo de cobre SFP+ 5m 10GbE |HP |
| 16. |10 Gbe |AP784A HP |Cabo Passivo de Cobre Passivo de Cobre 3m |HP |
| 17. |10 Gbe |AP785A HP |Cabo Passivo de Cobre Passivo de Cobre 5m |HP |
| 18. |10 Gbe |AP818A HP |Cabo Ativo de Cobre Ativo SFP+ de 1m da série B |HP |
| 19. |10 Gbe |AP819A HP |Cabo Ativo de Cobre Ativo SFP+ da série B de 3m |HP |
| 20. |10 Gbe |J9150A HP |X132 10G SFP+ LC SR Transceiver |HP |
| 21. |10 Gbe |J9151A HP |X132 10G SFP+ LC LR Transceiver |HP |
| 22. |10 Gbe |J9283B HP |X242 10G SFP+ SFP+ cabo DAC 3m |HP |
| 23. |10 Gbe |J9285B HP |X242 10G SFP+ SFP+ cabo DAC 7m |HP |
| 24. |10 Gbe |JD095B HP |X240 10G SFP+ SFP+ Cabo DAC de 0,65m |HP |
| 25. |10 Gbe |JD096B HP |X240 10G SFP+ SFP+ Cabo DAC de 1,2 m |HP |
| 26. |10 Gbe |JD097B HP |X240 10G SFP+ SFP+ cabo DAD 3m |HP |
| 27. |10 Gbe |MAM1Q00A-QSA Mellanox |Adaptador QSFP+ |Tecnologias Mellanox |
| 28. |10 Gbe |MC2309124-006 Mt |Cabo de cobre passivo 1x SFP+ A QSFP 10Gb/s 24awg 7m |Tecnologias Mellanox |
| 29. |10 Gbe |MC2309124-007 Mt |Cabo de cobre passivo 1x SFP+ A QSFP 10Gb/s 24awg 7m |Tecnologias Mellanox |
| 30. |10 Gbe |MC2309130-003 Mt |Cabo de cobre passivo 1x SFP+ A QSFP 10Gb/s 30awg 3m |Tecnologias Mellanox |
| 31. |10 Gbe |MC2309130-00A Mt |Cabo de cobre passivo 1x SFP+ A QSFP 10Gb/s 30awg 0,5m |Tecnologias Mellanox |
| 32. |10 Gbe |MC3309124-005 Mt |Cabo de cobre passivo 1x SFP+ 10Gb/s 24awg 5m |Tecnologias Mellanox |
| 33. |10 Gbe |MC3309124-007 Mt |Cabo de cobre passivo 1x SFP+ 10Gb/s 24awg 7m |Tecnologias Mellanox |
| 34. |10 Gbe |MC3309130-003 Mt |Cabo de cobre passivo 1x SFP+ 10Gb/s 30awg 3m |Tecnologias Mellanox |
| 35. |10 Gbe |MC3309130-00A Mt |Cabo de cobre passivo 1x SFP+ 10Gb/s 30awg 0,5m |Tecnologias Mellanox |

### <a name="switches-supported-by-mellanox"></a>Interruptores suportados pela Mellanox
A tabela que se segue lista os interruptores suportados pela Mellanox. Estes não foram testados pela Microsoft, mas são suscetíveis de funcionar com o seu dispositivo StorSimple.

| S. Não. | Velocidade | Modelação | Descrição | Criação |
| --- | --- | --- | --- | --- |
| 1. |10Gbe |516733-B21 |HP ProCurve 6120XG 10GbE Ethernet Blade Switch |HP |
| 2. |10Gbe |538113-B21 |Módulo de Pass-Through HP 10GbE (PTM) |HP |
| 3. |10Gbe |EN4093 |IBM PureFlex System Fabric EN4093 10 Gigabit Módulo de Comutação Escalável |IBM |
| 4. |1GbE |3020 |Lâmina de interruptor Cisco Catalyst 3020 1GbE |Cisco |
| 5. |1GbE |3020X |Lâmina de interruptor Cisco Catalyst 3020X 1GbE |Cisco |
| 6. |1GbE |438030-B21 |Módulo de interruptor HP 1GbE - Botão de lâmina De Camada 2/3 Ethernet |HP |
| 7. |1GbE |6120G |Hp ProCurve 6120G/XG 1GbE lâmina |HP |

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre os componentes e o estado do hardware StorSimple](./storsimple-8000-monitor-hardware-status.md).