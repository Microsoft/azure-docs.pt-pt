---
title: Interfaces de hardware para o StorSimple 10 GbE | Documentos da Microsoft
description: Descreve os transcetores (SFP) conectáveis do suportados pequenos, cabos e comutadores para as interfaces de rede de 10 GbE no dispositivo StorSimple.
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
ms.openlocfilehash: 8303195f0f3228ee145cbba9e322ea4e5e4c1264
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64726961"
---
# <a name="supported-hardware-for-the-10-gbe-network-interfaces-on-your-storsimple-device"></a>Hardware suportado para as interfaces de rede 10 GbE no dispositivo StorSimple
## <a name="overview"></a>Descrição geral
Este artigo fornece informações sobre hardware suplementar que funciona com o seu dispositivo do Microsoft Azure StorSimple.

## <a name="list-of-devices-tested-by-microsoft"></a>Lista de dispositivos testado pela Microsoft
Microsoft tem testado o seguintes transcetores (SFP) conectáveis pequenos, cabos e comutadores, para garantir que eles funcionem corretamente com dispositivos. (As tabelas seguintes serão atualizadas como novo hardware é testado.)

### <a name="sfp-transceivers"></a>SFP + Transcetores
| Criar | Modelo |
| --- | --- |
| Cisco |SFP-10G-SR |

### <a name="cables"></a>Cabos
| S. Não. | Criar | Modelo |
| --- | --- | --- |
| 1. |Cisco |SFP-H10GB-CU1M |
| 2. |Cisco |SFP-H10GB-CU2M |
| 3. |Cisco |SFP-H10GB-CU3M |
| 4. |Tripp-Lite |N820-05M (OM3) |

### <a name="switches"></a>Comutadores
| S. Não. | Criar | Modelo |
| --- | --- | --- |
| 1. |Cisco |N3K-C3172PQ-10GE |
| 2. |Cisco |N3K-C3048-ZM-F |
| 3. |Cisco |N5K-C5596UP-FA |

## <a name="list-of-devices-tested-in-the-field"></a>Lista de dispositivos testado no campo
Esta secção contém a lista de dispositivos que tenham sido implantados com êxito no campo pelos clientes do StorSimple. Estes não foram testados pela Microsoft, mas são provável que funcione com o seu dispositivo StorSimple.

| Parâmetro | Value |
| --- | --- |
| Certifique de comutador |Juniper |
| Modelo do comutador |ex4550-32F |
| Versão do sistema operativo de comutador |JunOS 12.3R9.4 |
| Modelo de painel |Carregar portas (PIC 0) |
| Marca de TRANSCEIVER |Juniper |
| Modelo de TRANSCEIVER |Número de peça 740 021308 <br></br> Número de peça 740 030658 |
| Versão de firmware TRANSCEIVER |Passe 01 versão 0,0 (comunicado) |
| Modelo de cabo |Duplex jumper LC/LC 50/125µ,   OM3, LSZH |
| Modelo do StorSimple |8600 |
| Versão de software do StorSimple |6.3.9600.17491 |

## <a name="list-of-devices-tested-by-oem-provider-mellanox"></a>Lista de dispositivos testadas pelo fornecedor de OEM (Mellanox)
Mellanox tem testado o seguintes transcetores (SFP) conectáveis pequenos, cabos e comutadores, para garantir que eles funcionem corretamente com Mellanox interfaces de rede, tais como as interfaces de rede de 10 GbE no dispositivo StorSimple.

### <a name="cables-and-modules-supported-by-mellanox"></a>Cabos e de módulos suportados pelo Mellanox
A tabela seguinte lista os cabos e módulos suportados pelo Mellanox. Estes não foram testados pela Microsoft, mas são provável que funcione com o seu dispositivo StorSimple.

| S. Não. | Velocidade | Modelo | Descrição | Criar |
| --- | --- | --- | --- | --- |
| 1. |10 GbE |CAB-SFP-SFP-1M |cabo de cobre passivo SFP + 10 Gb/s 1 milhão |Arista |
| 2. |10 GbE |CAB-SFP-SFP-2M |cabo de cobre passivo SFP + 10 Gb/s 2m |Arista |
| 3. |10 GbE |CAB-SFP-SFP-3M |cabo de cobre passivo SFP + 10 Gb/s 3m |Arista |
| 4. |10 GbE |CAB-SFP-SFP-5M |cabo de cobre passivo SFP + 10 Gb/s 5m |Arista |
| 5. |10 GbE |Cisco SFP-H10GBCU1M |Cabo SFP + Cisco |Cisco |
| 6. |10 GbE |Cisco SFP-H10GBCU3M |Cabo SFP + Cisco |Cisco |
| 7. |10 GbE |Cisco SFP-H10GBCU5M |Cabo SFP + Cisco |Cisco |
| 8. |10 GbE |J9281B HP X242 10G |SFP + para SFP + 1 milhão direto anexar o cabo de cobre |HP |
| 9. |10 GbE |455883 B21 HP BLc |10Gb SR SFP + optar ativamente por participar |HP |
| 10. |10 GbE |455886 B21 HP BLc |10Gb LR SFP + optar ativamente por participar |HP |
| 11. |10 GbE |487649 B21 HP BLc |Cobre de 10 gbe 0,5 m SFP + cabo |HP |
| 12. |10 GbE |487652 B21 HP BLc |Cobre de 10 gbe 1M SFP + cabo |HP |
| 13. |10 GbE |487655 B21 HP BLc |Cobre de 10 gbe 3M SFP + cabo |HP |
| 14. |10 GbE |487658 B21 HP BLc |Cobre de 10 gbe 7m SFP + cabo |HP |
| 15. |10 GbE |537963 B21 HP BLc |Cobre de 10 gbe 5m SFP + cabo |HP |
| 16. |10 GbE |AP784A HP |a 3M série C passivo cobre SFP + cabo |HP |
| 17. |10 GbE |AP785A HP |Cabo série C passivo cobre SFP + 5m |HP |
| 18. |10 GbE |AP818A HP |1 milhão de série B SFP cobre Active + cabo |HP |
| 19. |10 GbE |AP819A HP |a 3M série B SFP cobre Active + cabo |HP |
| 20. |10 GbE |J9150A HP |X132 10G SFP+ LC SR Transceiver |HP |
| 21. |10 GbE |J9151A HP |X132 10G SFP+ LC LR Transceiver |HP |
| 22. |10 GbE |J9283B HP |X242 10 G SFP + SFP + 3M DAC cabo |HP |
| 23. |10 GbE |J9285B HP |X242 10 G SFP + SFP + 7 m DAC cabo |HP |
| 24. |10 GbE |JD095B HP |X240 10 G SFP + SFP + 0.65 m DAC cabo |HP |
| 25. |10 GbE |JD096B HP |X240 10 G SFP + SFP + m de 1,2 DAC cabo |HP |
| 26. |10 GbE |JD097B HP |X240 10 G SFP + SFP + 3M DAD cabo |HP |
| 27. |10 GbE |MAM1Q00A-QSA Mellanox |QSFP a placa de SFP + |Tecnologias de Mellanox |
| 28. |10 GbE |MC2309124 006 Mt |Cabo de cobre passivo 1 x SFP+ para QSFP 10 Gb/s 24awg 7 m |Tecnologias de Mellanox |
| 29. |10 GbE |MC2309124 007 Mt |Cabo de cobre passivo 1 x SFP+ para QSFP 10 Gb/s 24awg 7 m |Tecnologias de Mellanox |
| 30. |10 GbE |MC2309130 003 Mt |Cabo de cobre passivo 1 x SFP+ para QSFP 10 Gb/s 30awg 3 m |Tecnologias de Mellanox |
| 31. |10 GbE |MC2309130-00A Mt |Cabo de cobre passivo 1 x SFP+ para QSFP 10 Gb/s 30awg 0,5 m |Tecnologias de Mellanox |
| 32. |10 GbE |MC3309124 005 Mt |Cabo de cobre passivo 1 24awg de 10 Gb/s de x SFP+ 5 m |Tecnologias de Mellanox |
| 33. |10 GbE |MC3309124 007 Mt |Cabo de cobre passivo 1 24awg de 10 Gb/s de x SFP+ 7 m |Tecnologias de Mellanox |
| 34. |10 GbE |MC3309130 003 Mt |Cabo de cobre passivo 1 30awg de 10 Gb/s de x SFP+ 3 m |Tecnologias de Mellanox |
| 35. |10 GbE |MC3309130 00A Mt |Cabo de cobre passivo 1 30awg de 10 Gb/s de x SFP+ 0,5 m |Tecnologias de Mellanox |

### <a name="switches-supported-by-mellanox"></a>Comutadores suportados pelo Mellanox
A tabela seguinte lista os comutadores suportados pelo Mellanox. Estes não foram testados pela Microsoft, mas são provável que funcione com o seu dispositivo StorSimple.

| S. Não. | Velocidade | Modelo | Descrição | Criar |
| --- | --- | --- | --- | --- |
| 1. |10GbE |516733-B21 |Comutador de painel de Ethernet de 10 gbe de ProCurve 6120XG de HP |HP |
| 2. |10GbE |538113-B21 |Módulo de pass-through de 10 gbe HP (PTM) |HP |
| 3. |10GbE |EN4093 |Módulo de comutador dimensionável do IBM PureFlex System recursos de infraestrutura EN4093 10 gigabits |IBM |
| 4. |1 gbe |3020 |Painel de comutador de 1 gbe de Catalyst 3020 Cisco |Cisco |
| 5. |1 gbe |3020X |Painel de comutador Cisco Catalyst 3020 X 1 gbe |Cisco |
| 6. |1 gbe |438030-B21 |Módulo de comutador de 1 gbe HP - Alternar painel de Ethernet de camada 2/3 do GbE2c |HP |
| 7. |1 gbe |6120G |Painel de comutador de 1 gbe 6120G ProCurve HP/XG |HP |

## <a name="next-steps"></a>Passos Seguintes
[Saiba mais sobre componentes de hardware do StorSimple e estado](storsimple-monitor-hardware-status.md).

