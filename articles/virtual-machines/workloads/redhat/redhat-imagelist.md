---
title: Imagens Red Hat Enterprise Linux disponíveis em Azure
description: Saiba mais sobre imagens Red Hat Enterprise Linux no Microsoft Azure
author: asinn826
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.openlocfilehash: 0a4d2ef5b7f367130151fabda3f1d97b65605931
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101676030"
---
# <a name="red-hat-enterprise-linux-rhel-images-available-in-azure"></a>Imagens red hat enterprise Linux (RHEL) disponíveis em Azure
O Azure oferece uma variedade de imagens RHEL para diferentes casos de uso.

> [!NOTE]
> Todas as imagens RHEL estão disponíveis nas nuvens públicas do Azure e do Governo Azure. Não estão disponíveis nas nuvens da China Azure.

## <a name="list-of-rhel-images"></a>Lista de imagens RHEL
Esta é uma lista de imagens RHEL disponíveis em Azure. Salvo indicação em contrário, todas as imagens são divididas em LVM e anexadas a repositórios RHEL regulares (não EUS, não E4S). As seguintes imagens estão atualmente disponíveis para uso geral:

> [!NOTE]
> As imagens RAW já não são produzidas a favor de imagens partidas pela LVM. A LVM oferece várias vantagens sobre o regime de partição em bruto (não-LVM) mais antigo, incluindo opções de redimensionamento de divisórias significativamente mais flexíveis.

Oferta| SKU | Criação de partições | Aprovisionamento | Notas
:----|:----|:-------------|:-------------|:-----
RHEL          | 6.7      | RAW    | Agente Linux | Suporte prolongado de ciclo de vida disponível a partir de 1 de dezembro. [Mais detalhes aqui.](redhat-extended-lifecycle-support.md)
|             | 6.8      | RAW    | Agente Linux | Suporte prolongado de ciclo de vida disponível a partir de 1 de dezembro. [Mais detalhes aqui.](redhat-extended-lifecycle-support.md)
|             | 6.9      | RAW    | Agente Linux | Suporte prolongado de ciclo de vida disponível a partir de 1 de dezembro. [Mais detalhes aqui.](redhat-extended-lifecycle-support.md)
|             | 6.10     | RAW    | Agente Linux | Suporte prolongado de ciclo de vida disponível a partir de 1 de dezembro. [Mais detalhes aqui.](redhat-extended-lifecycle-support.md)
|             | 7-RAW    | RAW    | Agente Linux | FAMÍLIA RHEL 7.x de imagens. <br> Anexado a repositórios regulares por defeito (não EUS).
|             | 7-LVM    | LVM    | Agente Linux | FAMÍLIA RHEL 7.x de imagens. <br> Anexado a repositórios regulares por defeito (não EUS). Se estiver à procura de uma imagem RHEL padrão para implementar, utilize este conjunto de imagens e/ou a sua contraparte geração 2.
|             | 7lvm-gen2| LVM    | Agente Linux | Geração 2, FAMÍLIA RHEL 7.x de imagens. <br> Anexado a repositórios regulares por defeito (não EUS). Se estiver à procura de uma imagem RHEL padrão para implementar, utilize este conjunto de imagens e/ou a sua contraparte geração 1.
|             | 7-RAW-CI | RAW-CI | nuvem-init  | FAMÍLIA RHEL 7.x de imagens. <br> Anexado a repositórios regulares por defeito (não EUS).
|             | 7.2      | RAW    | Agente Linux |
|             | 7.3      | RAW    | Agente Linux |
|             | 7.4      | RAW    | Agente Linux | Anexado aos repositórios da UE por defeito a partir de abril de 2019.
|             | 74-gen2  | RAW    | Agente Linux | Anexado aos repositórios eus por defeito.
|             | 7,5      | RAW    | Agente Linux | Anexado aos repositórios da UE por defeito a partir de junho de 2019.
|             | 75-gen2  | RAW    | Agente Linux | Anexado aos repositórios eus por defeito.
|             | 7.6      | RAW    | Agente Linux | Anexado aos repositórios da UE por defeito a partir de maio de 2019.
|             | 76-gen2  | RAW    | Agente Linux | Anexado aos repositórios eus por defeito.
|             | 7.7      | LVM    | Agente Linux | Anexado aos repositórios eus por defeito.
|             | 77-gen2  | LVM    | Agente Linux | Anexado aos repositórios eus por defeito.
|             | 7.8      | LVM    | Agente Linux | Anexado aos repositórios regulares (EUS indisponível para RHEL 7.8)
|             | 78-gen2  | LVM    | Agente Linux | Anexado aos repositórios regulares (EUS indisponível para RHEL 7.8)
|             | 7,9      | LVM    | Agente Linux | Anexado aos repositórios regulares (EUS indisponível para RHEL 7.9)
|             | 79-gen2  | LVM    | Agente Linux | Anexado aos repositórios regulares (EUS indisponível para RHEL 7.9)
|             | 8-LVM    | LVM    | Agente Linux | FAMÍLIA RHEL 8.x de imagens. Ligado a repositórios regulares.
|             | 8-lvm-gen2| LVM    | Agente Linux | Hiper-V Geração 2 - FAMÍLIA RHEL 8.x de imagens. Ligado a repositórios regulares.
|             | 8        | LVM    | Agente Linux | Imagens RHEL 8.0.
|             | 8-gen2   | LVM    | Agente Linux | Hiper-V Geração 2 - IMAGENS RHEL 8.0.
|             | 8.1      | LVM    | Agente Linux | Anexado aos repositórios eus por defeito.
|             | 81gen2   | LVM    | Agente Linux | Hiper-V Geração 2 - Anexa aos repositórios eus a partir de novembro de 2020.
|             | 8.1-ci   | LVM    | Agente Linux | Anexado aos repositórios da UE a partir de novembro de 2020.
|             | 81-ci-gen2| LVM    | Agente Linux | Hiper-V Geração 2 - Anexa aos repositórios eus a partir de novembro de 2020.
|             | 8.2      | LVM    | Agente Linux | Anexado aos repositórios da UE a partir de novembro de 2020.
|             | 82gen2   | LVM    | Agente Linux | Hiper-V Geração 2 - Anexa aos repositórios eus a partir de novembro de 2020.
|             | 8.3   | LVM    | Agente Linux |  Anexado aos repositórios regulares (EUS indisponível para RHEL 8.3)
|             | 83-gen2   | LVM    | Agente Linux |Hiper-V Geração 2 - Anexado a repositórios regulares (EUS indisponível para RHEL 8.3)
RHEL-SAP      | 7.4      | LVM    | Agente Linux | RHEL 7.4 para SAP HANA e Business Apps. Anexados aos repositórios E4S, cobrará um prémio pela SAP e pela RHEL, bem como pela taxa de cálculo base.
|             | 74sap-gen2| LVM    | Agente Linux | RHEL 7.4 para SAP HANA e Business Apps. Imagem de geração 2. Anexados aos repositórios E4S, cobrará um prémio pela SAP e pela RHEL, bem como pela taxa de cálculo base.
|             | 7,5       | LVM    | Agente Linux | RHEL 7.5 para SAP HANA e Business Apps. Anexados aos repositórios E4S, cobrará um prémio pela SAP e pela RHEL, bem como pela taxa de cálculo base.
|             | 75sap-gen2| LVM    | Agente Linux | RHEL 7.5 para SAP HANA e Business Apps. Imagem de geração 2. Anexados aos repositórios E4S, cobrará um prémio pela SAP e pela RHEL, bem como pela taxa de cálculo base.
|             | 7.6       | LVM    | Agente Linux | RHEL 7.6 para SAP HANA e Business Apps. Anexados aos repositórios E4S, cobrará um prémio pela SAP e pela RHEL, bem como pela taxa de cálculo base.
|             | 76sap-gen2| LVM    | Agente Linux | RHEL 7.6 para SAP HANA e Business Apps. Imagem de geração 2. Anexados aos repositórios E4S, cobrará um prémio pela SAP e pela RHEL, bem como pela taxa de cálculo base.
|             | 7.7       | LVM    | Agente Linux | RHEL 7.7 para SAP HANA e Business Apps. Anexados aos repositórios E4S, cobrará um prémio pela SAP e pela RHEL, bem como pela taxa de cálculo base.
RHEL-SAP-HANA (a ser removido em novembro de 2020) | 6.7       | RAW    | Agente Linux | RHEL 6.7 para SAP HANA. Antiquado a favor das imagens RHEL-SAP. Esta imagem será removida em novembro de 2020. Mais detalhes sobre as ofertas de nuvem SAP da Red Hat estão disponíveis [aqui.](https://access.redhat.com/articles/3751271)
|             | 7.2       | LVM    | Agente Linux | RHEL 7.2 para SAP HANA. Antiquado a favor das imagens RHEL-SAP. Esta imagem será removida em novembro de 2020. Mais detalhes sobre as ofertas de nuvem SAP da Red Hat estão disponíveis [aqui.](https://access.redhat.com/articles/3751271)
|             | 7.3       | LVM    | Agente Linux | RHEL 7.3 para SAP HANA. Antiquado a favor das imagens RHEL-SAP. Esta imagem será removida em novembro de 2020. Mais detalhes sobre as ofertas de nuvem SAP da Red Hat estão disponíveis [aqui.](https://access.redhat.com/articles/3751271)
RHEL-SAP-APPS | 6.8       | RAW    | Agente Linux | RHEL 6.8 para aplicações empresariais SAP. Antiquado a favor das imagens RHEL-SAP.
|             | 7.3       | LVM    | Agente Linux | RHEL 7.3 para aplicações empresariais SAP. Antiquado a favor das imagens RHEL-SAP.
|             | 7.4       | LVM    | Agente Linux | RHEL 7.4 para aplicações empresariais SAP.
|             | 7.6       | LVM    | Agente Linux | RHEL 7.6 para aplicações empresariais SAP.
|             | 7.7       | LVM    | Agente Linux | RHEL 7.7 para aplicações empresariais SAP.
|             | 77-gen2       | LVM    | Agente Linux | RHEL 7.7 para aplicações empresariais SAP. Imagem de geração 2
|             | 8.1       | LVM    | Agente Linux | RHEL 8.1 para aplicações empresariais SAP.
|             | 81-gen2      | LVM    | Agente Linux | RHEL 8.1 para aplicações empresariais SAP. Imagem de geração 2.
|             | 8.2       | LVM    | Agente Linux | RHEL 8.2 para aplicações empresariais SAP.
|             | 82-gen2      | LVM    | Agente Linux | RHEL 8.2 para aplicações empresariais SAP. Imagem de geração 2.
RHEL-HA       | 7.4       | LVM    | Agente Linux | RHEL 7.4 com HA Add-On. Cobrará um prémio por HA e RHEL em cima da taxa de cálculo base. Antiquado a favor das imagens RHEL-SAP-HA.
|             | 7,5       | LVM    | Agente Linux | RHEL 7.5 com HA Add-On. Cobrará um prémio por HA e RHEL em cima da taxa de cálculo base. Antiquado a favor das imagens RHEL-SAP-HA.
|             | 7.6       | LVM    | Agente Linux | RHEL 7.6 com HA Add-On. Cobrará um prémio por HA e RHEL em cima da taxa de cálculo base. Antiquado a favor das imagens RHEL-SAP-HA.
RHEL-SAP-HA   | 7.4          | LVM    | Agente Linux | RHEL 7.4 para SAP com serviços de HA e Atualização. Anexado aos repositórios E4S. Cobrará um prémio aos repositórios SAP e HA, bem como ao RHEL, para além das taxas de cálculo base.
|             | 74sapha-gen2 | LVM    | Agente Linux | RHEL 7.4 para SAP com serviços de HA e Atualização. Imagem de geração 2. Anexado aos repositórios E4S. Cobrará um prémio aos repositórios SAP e HA, bem como ao RHEL, para além das taxas de cálculo base.
|             | 7,5          | LVM    | Agente Linux | RHEL 7.5 para SAP com serviços de HA e Atualização. Anexado aos repositórios E4S. Cobrará um prémio aos repositórios SAP e HA, bem como ao RHEL, para além das taxas de cálculo base.
|             | 7.6          | LVM    | Agente Linux | RHEL 7.6 para SAP com serviços de HA e Atualização. Anexado aos repositórios E4S. Cobrará um prémio aos repositórios SAP e HA, bem como ao RHEL, para além das taxas de cálculo base.
|             | 76sapha-gen2 | LVM    | Agente Linux | RHEL 7.6 para SAP com serviços de HA e Atualização. Imagem de geração 2. Anexado aos repositórios E4S. Cobrará um prémio aos repositórios SAP e HA, bem como ao RHEL, para além das taxas de cálculo base.
|             | 7.7          | LVM    | Agente Linux | RHEL 7.7 para SAP com serviços de HA e Atualização. Anexado aos repositórios E4S. Cobrará um prémio aos repositórios SAP e HA, bem como ao RHEL, para além das taxas de cálculo base.
|             | 77sapha-gen2 | LVM    | Agente Linux | RHEL 7.7 para SAP com serviços de HA e Atualização. Imagem de geração 2. Anexado aos repositórios E4S. Cobrará um prémio aos repositórios SAP e HA, bem como ao RHEL, para além das taxas de cálculo base.
|             | 8.1          | LVM    | Agente Linux | RHEL 8.1 para SAP com serviços de HA e Atualização. Anexado aos repositórios E4S. Cobrará um prémio aos repositórios SAP e HA, bem como ao RHEL, para além das taxas de cálculo base.
|             | 81sapha-gen2          | LVM    | Agente Linux | RHEL 8.1 para SAP com serviços de HA e Atualização. Imagens de geração 2 anexadas aos repositórios E4S. Cobrará um prémio aos repositórios SAP e HA, bem como ao RHEL, para além das taxas de cálculo base.
|             | 8.2          | LVM    | Agente Linux | RHEL 8.2 para SAP com serviços de HA e Atualização. Cobrará um prémio aos repositórios SAP e HA, bem como ao RHEL, para além das taxas de cálculo base.
|             | 82sapha-gen2          | LVM    | Agente Linux | RHEL 8.2 para SAP com serviços de HA e Atualização. Imagens de geração 2 anexadas aos repositórios E4S. Cobrará um prémio aos repositórios SAP e HA, bem como ao RHEL, para além das taxas de cálculo base.
rhel-byos     |rhel-lvm74| LVM    | Agente Linux | As imagens RHEL 7.4 BYOS, não anexadas a nenhuma fonte de atualizações, não cobrarão um prémio RHEL.
|             |rhel-lvm75| LVM    | Agente Linux | As imagens RHEL 7.5 BYOS, não anexadas a nenhuma fonte de atualizações, não cobrarão um prémio RHEL.
|             |rhel-lvm76| LVM    | Agente Linux | As imagens RHEL 7.6 BYOS, não anexadas a nenhuma fonte de atualizações, não cobrarão um prémio RHEL.
|             |rhel-lvm76-gen2| LVM    | Agente Linux | As imagens DE 7.6 Geração 2 BYOS, não ligadas a nenhuma fonte de atualizações, não cobrarão um prémio RHEL.
|             |rhel-lvm77| LVM    | Agente Linux | As imagens RHEL 7.7 BYOS, não anexadas a nenhuma fonte de atualizações, não cobrarão um prémio RHEL.
|             |rhel-lvm77-gen2| LVM    | Agente Linux | As imagens DE 7.7 Geração 2 BYOS, não ligadas a nenhuma fonte de atualizações, não cobrarão um prémio RHEL.
|             |rhel-lvm78| LVM    | Agente Linux | As imagens RHEL 7.8 BYOS, não anexadas a nenhuma fonte de atualizações, não cobrarão um prémio RHEL.
|             |rhel-lvm78-gen2| LVM    | Agente Linux | As imagens DE 7.8 Geração 2 BYOS, não ligadas a nenhuma fonte de atualizações, não cobrarão um prémio RHEL.
|             |rhel-lvm8 | LVM    | Agente Linux | As imagens RHEL 8.0 BYOS , não anexadas a nenhuma fonte de atualizações, não cobrarão um prémio RHEL.
|             |rhel-lvm8-gen2 | LVM    | Agente Linux | As imagens REL 8.0 Generation 2 BYOS , não anexadas a nenhuma fonte de atualizações, não cobrarão um prémio RHEL.
|             |rhel-lvm81 | LVM    | Agente Linux | As imagens RHEL 8.1 BYOS , não anexadas a nenhuma fonte de atualizações, não cobrarão um prémio RHEL.
|             |rhel-lvm81-gen2 | LVM    | Agente Linux | As imagens REL 8.1 Generation 2 BYOS , não anexadas a nenhuma fonte de atualizações, não cobrarão um prémio RHEL.
|             |rhel-lvm82 | LVM    | Agente Linux | As imagens RHEL 8.2 BYOS , não anexadas a nenhuma fonte de atualizações, não cobrarão um prémio RHEL.
|             |rhel-lvm82-gen2 | LVM    | Agente Linux | As imagens REL 8.2 Generation 2 BYOS , não anexadas a nenhuma fonte de atualizações, não cobrarão um prémio RHEL.

> [!NOTE]
> A oferta de produtos RHEL-SAP-HANA é considerada fim de vida pela Red Hat. As implementações existentes continuarão a funcionar normalmente, mas a Red Hat recomenda que os clientes migram das imagens RHEL-SAP-HANA para as imagens RHEL-SAP-HA, que inclui os repositórios SAP HANA, bem como o addon HA. Mais detalhes sobre as ofertas de nuvem SAP da Red Hat estão disponíveis [aqui.](https://access.redhat.com/articles/3751271)

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre as [imagens do Chapéu Vermelho em Azure.](./redhat-images.md)
* Saiba mais sobre a [Infraestrutura de Atualização do Chapéu Vermelho.](./redhat-rhui.md)
* Saiba mais sobre a [oferta DA RHEL BYOS.](./byos.md)
* As informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL podem ser encontradas na página [Red Hat Enterprise Linux Life Cycle.](https://access.redhat.com/support/policy/updates/errata)
