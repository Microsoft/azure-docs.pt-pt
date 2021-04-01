---
title: O que é uma subreorigem de ligação de rede virtual de zonas privadas Azure DNS
description: Visão geral do sub recurso de ligação de rede virtual uma zona privada Azure DNS
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 5e43ad35cdaad89d5b9f43007bad8782f358c1a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94954346"
---
# <a name="what-is-a-virtual-network-link"></a>O que é uma ligação de rede virtual?

Uma vez que você cria uma zona de DNS privada em Azure, não é imediatamente acessível a partir de qualquer rede virtual. Deve ligá-lo a uma rede virtual antes que um VM alojado nessa rede possa aceder à zona privada de DNS.
Para ligar uma zona de DNS privada a uma rede virtual, tem de criar uma ligação de rede virtual sob a zona privada de DNS. Cada zona privada de DNS tem uma coleção de recursos infantis de ligação de rede virtual. Cada um destes recursos representa uma ligação a uma rede virtual.

Pode ligar uma rede virtual a uma zona privada de DNS como uma rede virtual de registo ou como uma rede virtual de resolução.

## <a name="registration-virtual-network"></a>Rede virtual de registo

Quando [cria uma ligação](./private-dns-getstarted-portal.md#link-the-virtual-network) entre uma zona privada de DNS e uma rede virtual, tem a opção de ligar [o registo automático](./private-dns-autoregistration.md) dos registos DNS para máquinas virtuais. Se escolher esta opção, a rede virtual torna-se uma rede virtual de registo para a zona privada de DNS. Um registo DNS é automaticamente criado para as máquinas virtuais que implementa na rede. Os registos DNS são criados para as máquinas virtuais que já implementou na rede virtual. Do ponto de vista da rede virtual, a zona privada de DNS torna-se a zona de registo daquela rede virtual.
Uma zona privada de DNS pode ter múltiplas redes virtuais de registo, no entanto cada rede virtual pode ter exatamente uma zona de registo associada a ela.

## <a name="resolution-virtual-network"></a>Rede virtual de resolução

Quando cria uma ligação de rede virtual sob uma zona privada de DNS e opta por não ativar o registo automático de registos dns, a rede virtual é tratada como uma rede virtual de resolução apenas. Os registos DNS para máquinas virtuais implementadas nessas redes não serão automaticamente criados na zona privada de DNS ligada. No entanto, as máquinas virtuais implantadas numa tal rede podem consultar com sucesso os registos DNS da zona privada de DNS. Estes registos podem ser criados manualmente por si ou podem ser povoados de outras redes virtuais que tenham sido ligadas como redes de registo com a zona privada de DNS.
Uma zona privada de DNS pode ter redes virtuais de resolução múltiplas e uma rede virtual pode ter várias zonas de resolução associadas a ela.

## <a name="limits"></a>Limites

Para entender quantas redes de registo e resolução, pode ligar-se a zonas privadas de DNS ver [Limites DNS do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-dns-limits)

## <a name="other-considerations"></a>Outras considerações

* As redes virtuais implementadas utilizando o modelo de implementação clássico não são suportadas.

* Pode criar apenas uma ligação entre uma zona privada de DNS e uma rede virtual.

* Cada ligação de rede virtual sob uma zona privada de DNS deve ter um nome único no contexto da zona privada de DNS. Você pode ter links com o mesmo nome em diferentes zonas privadas de DNS.

* Depois de criar uma ligação de rede virtual, verifique o campo "Link Status" do recurso de ligação de rede virtual. Dependendo do tamanho da rede virtual, pode demorar alguns minutos até que o link esteja em funcionamento e as alterações do Estado de Ligação para *Concluído*.

* Quando elimina uma rede virtual, todas as ligações de rede virtual e registos DNS auto-registados associados a ela em diferentes zonas privadas de DNS são automaticamente eliminadas.

## <a name="next-steps"></a>Passos seguintes

* Saiba como ligar uma rede virtual a uma zona privada de DNS utilizando [o portal Azure](./private-dns-getstarted-portal.md#link-the-virtual-network)

* Saiba como criar uma zona privada em Azure DNS utilizando [a Azure PowerShell](./private-dns-getstarted-powershell.md) ou [Azure CLI](./private-dns-getstarted-cli.md).

* Leia sobre [alguns cenários comuns de zona privada](./private-dns-scenarios.md) que podem ser realizados com zonas privadas em Azure DNS.

* Para perguntas e respostas comuns sobre zonas privadas em Azure DNS, incluindo comportamentos específicos que pode esperar para determinados tipos de operações, consulte [o DNS PRIVADO FAQ](./dns-faq-private.md).