---
title: Red Hat Enterprise Linux imagens no Azure | Microsoft Docs
description: Saiba mais sobre as imagens de Red Hat Enterprise Linux no Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/18/2019
ms.author: alsin
ms.openlocfilehash: 61991e271a68c9160a34d0de99fe4c9259ca41cb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75486342"
---
# <a name="red-hat-enterprise-linux-rhel-images-available-in-azure"></a>Red Hat Enterprise Linux (RHEL) imagens disponíveis no Azure
O Azure oferece uma variedade de imagens RHEL para diferentes casos de uso.

## <a name="list-of-rhel-images"></a>Lista de imagens RHEL
Esta é uma lista de imagens RHEL disponíveis no Azure. Salvo indicação em contrário, todas as imagens são particionadas por LVM e anexadas a repositórios RHEL regulares (não EUS, não E4S). As seguintes imagens estão disponíveis no momento para uso geral:

Oferta| SKU | Criação de partições | Aprovisionamento | Notas
:----|:----|:-------------|:-------------|:-----
RHEL          | 6.7      | RAW    | Agente Linux |
|             | 6.8      | RAW    | Agente Linux |
|             | 6.9      | RAW    | Agente Linux |
|             | 6.10     | RAW    | Agente Linux |
|             | 7-RAW    | RAW    | Agente Linux | Família de imagens RHEL 7. x. <br> Anexado a repositórios regulares por padrão (não EUS).
|             | 7-LVM    | LVM    | Agente Linux | Família de imagens RHEL 7. x. <br> Anexado a repositórios regulares por padrão (não EUS).
|             | 7-RAW-CI | RAW-CI | Cloud-init  | Família de imagens RHEL 7. x. <br> Anexado a repositórios regulares por padrão (não EUS).
|             | 7.2      | RAW    | Agente Linux |
|             | 7.3      | RAW    | Agente Linux |
|             | 7.4      | RAW    | Agente Linux | Anexado a repositórios do EUS por padrão a partir de abril de 2019.
|             | 7.5      | RAW    | Agente Linux | Anexado a repositórios do EUS por padrão a partir de junho de 2019.
|             | 7.6      | RAW    | Agente Linux | Anexado a repositórios do EUS por padrão a partir de maio de 2019.
|             | 7.7      | LVM    | Agente Linux | Anexado a repositórios do EUS por padrão.
|             | 8        | LVM    | Agente Linux | Família de imagens RHEL 8. x
|             | 8-Gen2   | LVM    | Agente Linux | Hyper-V geração 2-família de imagens RHEL 8. x.
RHEL-SAP      | 7.4      | LVM    | Agente Linux | RHEL 7,4 para aplicativos de SAP HANA e de negócios. Anexados aos repositórios do E4S, o cobrará um Premium para SAP e RHEL, bem como a taxa de computação base.
|             | 7.5      | LVM    | Agente Linux | RHEL 7,5 para aplicativos de SAP HANA e de negócios. Anexados aos repositórios do E4S, o cobrará um Premium para SAP e RHEL, bem como a taxa de computação base.
|             | 7.6      | LVM    | Agente Linux | RHEL 7,5 para aplicativos de SAP HANA e de negócios. Anexados aos repositórios do E4S, o cobrará um Premium para SAP e RHEL, bem como a taxa de computação base.
|             | 7.7      | LVM    | Agente Linux | RHEL 7,5 para aplicativos de SAP HANA e de negócios. Anexados aos repositórios do E4S, o cobrará um Premium para SAP e RHEL, bem como a taxa de computação base.
RHEL-SAP-HANA | 6.7      | RAW    | Agente Linux | RHEL 6,7 para SAP HANA. Desatualizado em favor das imagens RHEL-SAP.
|             | 7.2      | LVM    | Agente Linux | RHEL 7,2 para SAP HANA. Desatualizado em favor das imagens RHEL-SAP.
|             | 7.3      | LVM    | Agente Linux | RHEL 7,3 para SAP HANA. Desatualizado em favor das imagens RHEL-SAP.
RHEL-SAP-APPS | 6.8      | RAW    | Agente Linux | RHEL 6,8 para SAP Business Applications. Desatualizado em favor das imagens RHEL-SAP.
|             | 7.3      | LVM    | Agente Linux | RHEL 7,3 para SAP Business Applications. Desatualizado em favor das imagens RHEL-SAP.
RHEL-HA       | 7.4      | LVM    | Agente Linux | RHEL 7,4 com complemento de alta disponibilidade. Cobrará uma Premium para HA e RHEL sobre a taxa de computação base.
|             | 7.5      | LVM    | Agente Linux | RHEL 7,5 com complemento de alta disponibilidade. Cobrará uma Premium para HA e RHEL sobre a taxa de computação base.
|             | 7.6      | LVM    | Agente Linux | RHEL 7,6 com complemento de alta disponibilidade. Cobrará uma Premium para HA e RHEL sobre a taxa de computação base.
RHEL-SAP-HA   | 7.4      | LVM    | Agente Linux | RHEL 7,4 para SAP com complemento de HA. Anexado a repositórios do E4S. Cobrará um prêmio Premium para repositórios SAP e HA, bem como RHEL, sobre as taxas de computação base.
|             | 7.5      | LVM    | Agente Linux | RHEL 7,5 para SAP com complemento de HA. Anexado a repositórios do E4S. Cobrará um prêmio Premium para repositórios SAP e HA, bem como RHEL, sobre as taxas de computação base.
|             | 7.6      | LVM    | Agente Linux | RHEL 7,6 para SAP com complemento de HA. Anexado a repositórios do E4S. Cobrará um prêmio Premium para repositórios SAP e HA, bem como RHEL, sobre as taxas de computação base.
RHEL-BYOS     |RHEL-lvm74| LVM    | Agente Linux | As imagens RHEL 7,4 BYOS, não conectadas a nenhuma fonte de atualizações, não cobrarão um RHEL Premium.
|             |RHEL-lvm75| LVM    | Agente Linux | As imagens RHEL 7,5 BYOS, não conectadas a nenhuma fonte de atualizações, não cobrarão um RHEL Premium.
|             |RHEL-lvm76| LVM    | Agente Linux | As imagens RHEL 7,6 BYOS, não conectadas a nenhuma fonte de atualizações, não cobrarão um RHEL Premium.
|             |RHEL-lvm77| LVM    | Agente Linux | As imagens RHEL 7,7 BYOS, não conectadas a nenhuma fonte de atualizações, não cobrarão um RHEL Premium.
|             |RHEL-lvm8 | LVM    | Agente Linux | As imagens RHEL 8 BYOS (a versão secundária do RHEL é mostrada no valor da versão da imagem), não anexada a nenhuma fonte de atualizações, não cobrará um RHEL Premium.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre as [imagens do Red Hat no Azure](./redhat-images.md).
* Saiba mais sobre a [infraestrutura de atualização do Red Hat](./redhat-rhui.md).
* Saiba mais sobre a [oferta BYOS do RHEL](./redhat-byos.md).
* Informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL podem ser encontradas na página [ciclo de vida Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) .