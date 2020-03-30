---
title: O que é um subrecurso de ligação de rede virtual de zonas privadas Azure DNS
description: Visão geral da ligação de rede virtual subrecurso a zona privada Azure DNS
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 9181ef93dfedbc28b297bef48a0bc37ba6d69798
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646766"
---
# <a name="what-is-a-virtual-network-link"></a>O que é uma ligação de rede virtual?

Uma vez que você cria uma zona Privada DNS em Azure, não é imediatamente acessível a partir de qualquer rede virtual. Deve ligá-lo a uma rede virtual antes que um VM hospedado nessa rede possa aceder à zona privada de DNS.
Para ligar uma zona privada de DNS a uma rede virtual, deve criar uma ligação de rede virtual sob a zona privada de DNS. Todas as zonas privadas de DNS têm uma coleção de recursos infantis de ligação de rede virtual. Cada um destes recursos representa uma ligação a uma rede virtual.

Pode ligar uma rede virtual a uma zona privada de DNS como uma rede virtual de registo ou como uma rede virtual de resolução.

## <a name="registration-virtual-network"></a>Rede virtual de registo

Ao [criar uma ligação](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network) entre uma zona Privada de DNS e uma rede virtual, tem a opção de ativar o [registo automático](./private-dns-autoregistration.md) de registos DNS para máquinas virtuais. Se escolher esta opção, a rede virtual torna-se uma rede virtual de registo para a zona privada de DNS. É criado automaticamente um registo DNS para as máquinas virtuais que se implanta na rede. Os registos DNS são criados para as máquinas virtuais que já implementou na rede virtual. Do ponto de vista da rede virtual, a zona privada de DNS torna-se a zona de registo dessa rede virtual.
Uma zona privada de DNS pode ter várias redes virtuais de registo, no entanto, cada rede virtual pode ter exatamente uma zona de registo associada a ela.

## <a name="resolution-virtual-network"></a>Rede virtual de resolução

Quando cria uma ligação de rede virtual sob uma zona privada de DNS e opta por não permitir o registo automático de registo saquede DNS, a rede virtual é tratada como uma resolução apenas uma rede virtual. Os registos dNS de máquinas virtuais implantadas nessas redes não serão automaticamente criados na zona privada de DNS ligada. No entanto, as máquinas virtuais implantadas numa tal rede podem consultar com sucesso os registos dNS da zona privada de DNS. Estes registos podem ser criados manualmente por si ou podem ser preenchidos a partir de outras redes virtuais que foram ligadas como redes de registo com a zona privada de DNS.
Uma zona privada de DNS pode ter múltiplas redes virtuais de resolução e uma rede virtual pode ter múltiplas zonas de resolução associadas a ela.

## <a name="limits"></a>Limites

Para entender quantas redes de registo e resolução, pode ligar-se a zonas privadas de DNS ver [Limites DNS Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits)

## <a name="other-considerations"></a>Outras considerações

* As redes virtuais implantadas utilizando modelos de implantação clássicos não são suportadas.

* Só pode criar uma ligação entre uma zona privada de DNS e uma rede virtual.

* Cada ligação de rede virtual sob uma zona privada de DNS deve ter um nome único no contexto da zona privada de DNS. Você pode ter links com o mesmo nome em diferentes zonas privadas de DNS.

* Depois de criar uma ligação de rede virtual, verifique o campo "Link Status" do recurso de ligação de rede virtual. Dependendo do tamanho da rede virtual, pode demorar alguns minutos até que o link esteja ligado e o Estado de Ligação muda para *Concluído*.

* Ao eliminar uma rede virtual, todas as ligações de rede virtuais e registos DNS registados automaticamente associados a ela em diferentes zonas privadas de DNS são automaticamente eliminadas.

## <a name="next-steps"></a>Passos seguintes

* Saiba como ligar uma rede virtual a uma zona privada de DNS usando [o portal Azure](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network)

* Saiba como criar uma zona privada em Azure DNS utilizando [o Azure PowerShell](./private-dns-getstarted-powershell.md) ou [o Azure CLI](./private-dns-getstarted-cli.md).

* Leia sobre [alguns cenários de zona privada](./private-dns-scenarios.md) comum que podem ser realizados com zonas privadas em DNS Azure.

* Para perguntas e respostas comuns sobre zonas privadas em DNS Azure, incluindo comportamentos específicos que pode esperar para determinados tipos de operações, consulte o [DNS faQ privado](./dns-faq-private.md).
