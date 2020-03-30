---
title: Imagens red hat enterprise linux em Azure Microsoft Docs
description: Conheça as imagens do Red Hat Enterprise Linux no Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: e6109a87750e588b12bfc9836c5db3db55420ec2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77133807"
---
# <a name="red-hat-enterprise-linux-rhel-images-available-in-azure"></a>Imagens da Red Hat Enterprise Linux (RHEL) disponíveis no Azure
O Azure oferece uma variedade de imagens RHEL para diferentes casos de utilização.

> [!NOTE]
> Todas as imagens RHEL estão disponíveis nas nuvens públicas de Azure e do Governo azure. Não estão disponíveis nas nuvens da China Azul.

## <a name="list-of-rhel-images"></a>Lista de imagens RHEL
Esta é uma lista de imagens RHEL disponíveis em Azure. Salvo indicação em contrário, todas as imagens são divididas em LVM e anexadas a repositórios rhel regulares (não EUS, não E4S). As seguintes imagens estão atualmente disponíveis para uso geral:

Oferta| SKU | Criação de partições | Aprovisionamento | Notas
:----|:----|:-------------|:-------------|:-----
RHEL          | 6.7      | CRU    | Agente Linux |
|             | 6.8      | CRU    | Agente Linux |
|             | 6.9      | CRU    | Agente Linux |
|             | 6.10     | CRU    | Agente Linux |
|             | 7-RAW    | CRU    | Agente Linux | Família de imagens RHEL 7.x. <br> Anexado a repositórios regulares por defeito (não EUS).
|             | 7-LVM    | LVM    | Agente Linux | Família de imagens RHEL 7.x. <br> Anexado a repositórios regulares por defeito (não EUS). Se procura uma imagem RHEL padrão para implementar, utilize este conjunto de imagens e/ou a sua congénere da Geração 2.
|             | 7lvm-gen2| LVM    | Agente Linux | Geração 2, Família de imagens RHEL 7.x. <br> Anexado a repositórios regulares por defeito (não EUS). Se procura uma imagem RHEL padrão para implementar, utilize este conjunto de imagens e/ou a sua congénere da Geração 1.
|             | 7-RAW-CI | RAW-CI | nuvem-init  | Família de imagens RHEL 7.x. <br> Anexado a repositórios regulares por defeito (não EUS).
|             | 7.2      | CRU    | Agente Linux |
|             | 7.3      | CRU    | Agente Linux |
|             | 7.4      | CRU    | Agente Linux | Anexados aos repositórios da UES por defeito a partir de abril de 2019.
|             | 74-gen2  | CRU    | Agente Linux | Anexado aos repositórios da EUS por defeito.
|             | 7,5      | CRU    | Agente Linux | Anexados aos repositórios da UES por incumprimento a partir de junho de 2019.
|             | 75-gen2  | CRU    | Agente Linux | Anexado aos repositórios da EUS por defeito.
|             | 7.6      | CRU    | Agente Linux | Anexados aos repositórios da UES por defeito a partir de maio de 2019.
|             | 76-gen2  | CRU    | Agente Linux | Anexado aos repositórios da EUS por defeito.
|             | 7.7      | LVM    | Agente Linux | Anexado aos repositórios da EUS por defeito.
|             | 8        | LVM    | Agente Linux | RHEL 8.x família de imagens
|             | 8 gen2   | LVM    | Agente Linux | Hyper-V Geração 2 - RHEL 8.x família de imagens.
RHEL-SAP      | 7.4      | LVM    | Agente Linux | RHEL 7.4 para SAP HANA e Aplicativos empresariais. Anexado aos repositórios E4S, cobrará um prémio pela SAP e RHEL, bem como pela taxa de cálculo base.
|             | 74sap-gen2| LVM    | Agente Linux | RHEL 7.4 para SAP HANA e Aplicativos empresariais. Imagem da geração 2. Anexado aos repositórios E4S, cobrará um prémio pela SAP e RHEL, bem como pela taxa de cálculo base.
|             | 7,5       | LVM    | Agente Linux | RHEL 7.5 para SAP HANA e Aplicativos empresariais. Anexado aos repositórios E4S, cobrará um prémio pela SAP e RHEL, bem como pela taxa de cálculo base.
|             | 75sap-gen2| LVM    | Agente Linux | RHEL 7.5 para SAP HANA e Aplicativos empresariais. Imagem da geração 2. Anexado aos repositórios E4S, cobrará um prémio pela SAP e RHEL, bem como pela taxa de cálculo base.
|             | 7.6       | LVM    | Agente Linux | RHEL 7.6 para SAP HANA e Aplicativos empresariais. Anexado aos repositórios E4S, cobrará um prémio pela SAP e RHEL, bem como pela taxa de cálculo base.
|             | 76sap-gen2| LVM    | Agente Linux | RHEL 7.6 para SAP HANA e Aplicativos empresariais. Imagem da geração 2. Anexado aos repositórios E4S, cobrará um prémio pela SAP e RHEL, bem como pela taxa de cálculo base.
|             | 7.7       | LVM    | Agente Linux | RHEL 7.7 para SAP HANA e Aplicativos empresariais. Anexado aos repositórios E4S, cobrará um prémio pela SAP e RHEL, bem como pela taxa de cálculo base.
RHEL-SAP-HANA | 6.7       | CRU    | Agente Linux | RHEL 6.7 para SAP HANA. Ultrapassado a favor das imagens RHEL-SAP.
|             | 7.2       | LVM    | Agente Linux | RHEL 7.2 para SAP HANA. Ultrapassado a favor das imagens RHEL-SAP.
|             | 7.3       | LVM    | Agente Linux | RHEL 7.3 para SAP HANA. Ultrapassado a favor das imagens RHEL-SAP.
RHEL-SAP-APPS | 6.8       | CRU    | Agente Linux | RHEL 6.8 para Aplicações Empresariais SAP. Ultrapassado a favor das imagens RHEL-SAP.
|             | 7.3       | LVM    | Agente Linux | RHEL 7.3 para Aplicações Empresariais SAP. Ultrapassado a favor das imagens RHEL-SAP.
RHEL-HA       | 7.4       | LVM    | Agente Linux | RHEL 7.4 com ADD-On HA. Cobrará um prémio por HA e RHEL em cima da taxa de cálculo base.
|             | 7,5       | LVM    | Agente Linux | RHEL 7.5 com Add-On HA. Cobrará um prémio por HA e RHEL em cima da taxa de cálculo base.
|             | 7.6       | LVM    | Agente Linux | RHEL 7.6 com Add-On HA. Cobrará um prémio por HA e RHEL em cima da taxa de cálculo base.
RHEL-SAP-HA   | 7.4          | LVM    | Agente Linux | RHEL 7.4 para SAP com HA e Serviços de Atualização. Ligado aos repositórios E4S. Cobrará um prémio pelos repositórios SAP e HA, bem como rHEL, além das taxas de computação base.
|             | 74sapha-gen2 | LVM    | Agente Linux | RHEL 7.4 para SAP com HA e Serviços de Atualização. Imagem da geração 2. Ligado aos repositórios E4S. Cobrará um prémio pelos repositórios SAP e HA, bem como rHEL, além das taxas de computação base.
|             | 7,5          | LVM    | Agente Linux | RHEL 7.5 para SAP com HA e Serviços de Atualização. Ligado aos repositórios E4S. Cobrará um prémio pelos repositórios SAP e HA, bem como rHEL, além das taxas de computação base.
|             | 7.6          | LVM    | Agente Linux | RHEL 7.6 para SAP com HA e Serviços de Atualização. Ligado aos repositórios E4S. Cobrará um prémio pelos repositórios SAP e HA, bem como rHEL, além das taxas de computação base.
|             | 76sapha-gen2 | LVM    | Agente Linux | RHEL 7.6 para SAP com HA e Serviços de Atualização. Imagem da geração 2. Ligado aos repositórios E4S. Cobrará um prémio pelos repositórios SAP e HA, bem como rHEL, além das taxas de computação base.
|             | 7.7          | LVM    | Agente Linux | RHEL 7.7 para SAP com HA e Serviços de Atualização. Ligado aos repositórios E4S. Cobrará um prémio pelos repositórios SAP e HA, bem como rHEL, além das taxas de computação base.
|             | 77sapha-gen2 | LVM    | Agente Linux | RHEL 7.7 para SAP com HA e Serviços de Atualização. Imagem da geração 2. Ligado aos repositórios E4S. Cobrará um prémio pelos repositórios SAP e HA, bem como rHEL, além das taxas de computação base.
rhel-byos     |rhel-lvm74| LVM    | Agente Linux | As imagens RHEL 7.4 BYOS, não ligadas a qualquer fonte de atualizações, não cobrarão um prémio RHEL.
|             |rhel-lvm75| LVM    | Agente Linux | As imagens RHEL 7.5 BYOS, não ligadas a qualquer fonte de atualizações, não cobrarão um prémio RHEL.
|             |rhel-lvm76| LVM    | Agente Linux | As imagens RHEL 7.6 BYOS, não ligadas a qualquer fonte de atualizações, não cobrarão um prémio RHEL.
|             |rhel-lvm77| LVM    | Agente Linux | As imagens RHEL 7.7 BYOS, não ligadas a qualquer fonte de atualizações, não cobrarão um prémio RHEL.
|             |rhel-lvm8 | LVM    | Agente Linux | As imagens RHEL 8 BYOS (versão rHEL menor é mostrada no valor da versão de imagem), não anexada a qualquer fonte de atualizações, não cobrarão um prémio RHEL.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre as [imagens do Chapéu Vermelho em Azure.](./redhat-images.md)
* Saiba mais sobre a Infraestrutura de Atualização do [Chapéu Vermelho.](./redhat-rhui.md)
* Saiba mais sobre a [oferta RHEL BYOS.](./byos.md)
* As informações sobre as políticas de suporte do Chapéu Vermelho para todas as versões do RHEL podem ser encontradas na página [red hat enterprise Linux Life Cycle.](https://access.redhat.com/support/policy/updates/errata)
