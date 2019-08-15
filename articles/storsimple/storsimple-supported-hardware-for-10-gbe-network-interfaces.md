---
title: Hardware para interfaces do StorSimple 10 GbE | Microsoft Docs
description: Descreve os transceptores, cabos e comutadores de fator de forma pequeno (SFP) com suporte para as interfaces de rede de 10 GbE em seu dispositivo StorSimple.
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
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68965020"
---
# <a name="supported-hardware-for-the-10-gbe-network-interfaces-on-your-storsimple-device"></a>Hardware com suporte para as interfaces de rede de 10 GbE em seu dispositivo StorSimple

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Descrição geral
Este artigo fornece informações sobre o hardware suplementar que funciona com seu dispositivo Microsoft Azure StorSimple.

## <a name="list-of-devices-tested-by-microsoft"></a>Lista de dispositivos testados pela Microsoft
A Microsoft testou os seguintes transceptores, cabos e comutadores conectável (SFP) de fator forma pequeno para garantir que eles funcionem de maneira ideal com os dispositivos. (As tabelas a seguir serão atualizadas conforme o novo hardware é testado.)

### <a name="sfp-transceivers"></a>SFP + transceptores
| Criar | Modelo |
| --- | --- |
| Cisco |SFP-10G-SR |

### <a name="cables"></a>Cabos
| S. Não. | Criar | Modelo |
| --- | --- | --- |
| 1. |Cisco |SFP-H10GB-CU1M |
| 2. |Cisco |SFP-H10GB-CU2M |
| 3. |Cisco |SFP-H10GB-CU3M |
| 4. |Ida-Lite |N820-05M (OM3) |

### <a name="switches"></a>Comutadores
| S. Não. | Criar | Modelo |
| --- | --- | --- |
| 1. |Cisco |N3K-C3172PQ-10GE |
| 2. |Cisco |N3K-C3048-ZM-F |
| 3. |Cisco |N5K-C5596UP-FA |

## <a name="list-of-devices-tested-in-the-field"></a>Lista de dispositivos testados no campo
Esta seção contém a lista de dispositivos que foram implantados com êxito no campo por clientes do StorSimple. Eles não foram testados pela Microsoft, mas provavelmente funcionarão com seu dispositivo StorSimple.

| Parâmetro | Value |
| --- | --- |
| Alternar marca |Juniper |
| Alternar modelo |ex4550-32F |
| Alternar versão do sistema operacional |JunOS 12.3R9.4 |
| Modelo de folha |Portas integradas (PIC 0) |
| Make de transceptor |Juniper |
| Modelo de transceptor |Peça número 740-021308 <br></br> Peça número 740-030658 |
| Versão do firmware do transceptor |Rev 01 versão 0,0 (relatada) |
| Modelo de cabo |Jumper duplex LC/LC 50/125μ, OM3, LSZH |
| Modelo do StorSimple |8600 |
| Versão do software StorSimple |6.3.9600.17491 |

## <a name="list-of-devices-tested-by-oem-provider-mellanox"></a>Lista de dispositivos testados pelo provedor OEM (Mellanox)
O Mellanox testou os seguintes transceptores, cabos e comutadores de fator forma pequeno conectável (SFP) para garantir que funcionem de forma ideal com interfaces de rede Mellanox, como as interfaces de rede de 10 GbE em seu dispositivo StorSimple.

### <a name="cables-and-modules-supported-by-mellanox"></a>Cabos e módulos com suporte do Mellanox
A tabela a seguir lista os cabos e os módulos suportados pelo Mellanox. Eles não foram testados pela Microsoft, mas provavelmente funcionarão com seu dispositivo StorSimple.

| S. Não. | Velocidade | Modelo | Descrição | Criar |
| --- | --- | --- | --- | --- |
| 1. |10 GbE |CAB-SFP-SFP-1M |cabo de cobre passivo SFP + 10 GB/s 1m |Arista |
| 2. |10 GbE |CAB-SFP-SFP-2M |cabo de cobre passivo SFP + 10 GB/s 2m |Arista |
| 3. |10 GbE |CAB-SFP-SFP-3M |cabo de cobre passivo SFP + 10 GB/s 3M |Arista |
| 4. |10 GbE |CAB-SFP-SFP-5 MIN |cabo de cobre passivo SFP + 10 GB/s 5 min |Arista |
| 5. |10 GbE |Cisco SFP-H10GBCU1M |Cabo Cisco SFP + |Cisco |
| 6. |10 GbE |Cisco SFP-H10GBCU3M |Cabo Cisco SFP + |Cisco |
| 7. |10 GbE |Cisco SFP-H10GBCU5M |Cabo Cisco SFP + |Cisco |
| 8. |10 GbE |J9281B HP X242 10G |SFP + para SFP + 1m de cabo de cobre de conexão direta |EMPRESA |
| 9. |10 GbE |455883-B21 HP BLc |10Gb SR SFP + opt |EMPRESA |
| 10. |10 GbE |455886-B21 HP BLc |10Gb LR SFP + opt |EMPRESA |
| 11. |10 GbE |487649-B21 HP BLc |Cabo de cobre SFP + 0,5 m 10Gbe |EMPRESA |
| 12. |10 GbE |487652-B21 HP BLc |Cabo de cobre SFP + 1m 10Gbe |EMPRESA |
| 13. |10 GbE |487655-B21 HP BLc |Cabo de cobre SFP + 3M 10Gbe |EMPRESA |
| 14. |10 GbE |487658-B21 HP BLc |Cabo de cobre SFP + 7m 10Gbe |EMPRESA |
| 15. |10 GbE |537963-B21 HP BLc |Cabo de cobre SFP + 5 min 10Gbe |EMPRESA |
| 16. |10 GbE |AP784A HP |Cabo SFP + de cobre 3M C-Series passivo |EMPRESA |
| 17. |10 GbE |AP785A HP |Cabo SFP + de cobre 5 min C-Series passivo |EMPRESA |
| 18. |10 GbE |AP818A HP |1 milhão de cabo SFP + de cobre as séries B |EMPRESA |
| 19. |10 GbE |AP819A HP |Cabo SFP + de cobre ativo da série B 3M |EMPRESA |
| 20. |10 GbE |J9150A HP |Transceptor X132 10G SFP + LC SR |EMPRESA |
| 21. |10 GbE |J9151A HP |X132 10G SFP + transceptor LC LR |EMPRESA |
| 22. |10 GbE |J9283B HP |X242 10G SFP + SFP + cabo DAC 3M |EMPRESA |
| 23. |10 GbE |J9285B HP |X242 10G SFP + SFP + cabo DAC 7m |EMPRESA |
| 24. |10 GbE |JD095B HP |Cabo DAC x240 10G SFP + SFP + 0.65 m |EMPRESA |
| 25. |10 GbE |JD096B HP |Cabo DAC x240 10G SFP + SFP + 1.2 m |EMPRESA |
| 26. |10 GbE |JD097B HP |X240 10G SFP + SFP + cabo pai do 3M |EMPRESA |
| 27. |10 GbE |MAM1Q00A-QSA Mellanox |QSFP para SFP + Adapter |Tecnologias Mellanox |
| 28. |10 GbE |MC2309124-006 MT |Cabo de cobre passivo 1x SFP + to QSFP 10Gb/s 24AWG 7m |Tecnologias Mellanox |
| 29. |10 GbE |MC2309124-007 MT |Cabo de cobre passivo 1x SFP + to QSFP 10Gb/s 24AWG 7m |Tecnologias Mellanox |
| 30. |10 GbE |MC2309130-003 MT |Cabo de cobre passivo 1x SFP + to QSFP 10Gb/s 30AWG 3M |Tecnologias Mellanox |
| 31. |10 GbE |MC2309130-00A MT |Cabo de cobre passivo 1x SFP + to QSFP 10Gb/s 30AWG 0,5 m |Tecnologias Mellanox |
| 32. |10 GbE |MC3309124-005 MT |Cabo de cobre passivo 1x SFP + 10Gb/s 24AWG 5 min |Tecnologias Mellanox |
| 33. |10 GbE |MC3309124-007 MT |Cabo de cobre passivo 1x SFP + 10Gb/s 24AWG 7m |Tecnologias Mellanox |
| 34. |10 GbE |MC3309130-003 MT |Cabo de cobre passivo 1x SFP + 10Gb/s 30AWG 3M |Tecnologias Mellanox |
| 35. |10 GbE |MC3309130-00A MT |Cabo de cobre passivo 1x SFP + 10Gb/s 30AWG 0,5 m |Tecnologias Mellanox |

### <a name="switches-supported-by-mellanox"></a>Opções compatíveis com o Mellanox
A tabela a seguir lista os comutadores suportados pelo Mellanox. Eles não foram testados pela Microsoft, mas provavelmente funcionarão com seu dispositivo StorSimple.

| S. Não. | Velocidade | Modelo | Descrição | Criar |
| --- | --- | --- | --- | --- |
| 1. |10 GbE |516733-B21 |Comutador de lâmina Ethernet HP Procurve 6120XG 10Gbe |EMPRESA |
| 2. |10 GbE |538113-B21 |Módulo de passagem HP 10Gbe (PTM) |EMPRESA |
| 3. |10 GbE |EN4093 |Módulo de comutador escalonável do IBM PureFlex System Fabric EN4093 10 Gigabit |IBM |
| 4. |1 GbE |3020 |Lâmina do comutador do Cisco Catalyst 3020 1 GbE |Cisco |
| 5. |1 GbE |3020X |Cisco Catalyst 3020X 1 GbE switch Blade |Cisco |
| 6. |1 GbE |438030-B21 |Módulo de comutador HP 1 GbE-comutador Ethernet GbE2c camada 2/3 |EMPRESA |
| 7. |1 GbE |6120G |HP Procurve 6120G/XG 1 GbE switch Blade |EMPRESA |

## <a name="next-steps"></a>Passos Seguintes
[Saiba mais sobre os componentes de hardware e o status do StorSimple](storsimple-monitor-hardware-status.md).

