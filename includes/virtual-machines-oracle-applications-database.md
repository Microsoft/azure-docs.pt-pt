---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 8106b1a77f11874b1b13f41bfc9e62ea800afc98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68361534"
---
### <a name="database-tier"></a>Nível de base de dados

O nível base de dados contém as instâncias de base de dados para a aplicação. A base de dados pode ser um sistema oracle DB, Oracle RAC ou Oracle Exadata Database. 

Se a escolha for utilizar o Oracle DB, a instância de base de dados pode ser implementada no Azure através das imagens Oracle DB disponíveis no Mercado Azure. Em alternativa, pode utilizar a interligação entre o Azure e o OCI para implantar o Oracle DB num modelo PaaS no OCI.

Para a Oracle RAC, pode implantar o Oracle RAC no Azure CloudSimple no modelo IaaS ou no OCI no modelo PaaS. Recomenda-se que utilize um sistema RAC de dois nós. 

Finalmente, para os sistemas Exadata, utilize o OCI interligar e implementar o sistema Exadata no OCI. O diagrama de arquitetura anterior acima mostra um sistema Exadata implantado em OCI em duas subnets.

Para cenários de produção, implemente múltiplas instâncias da base de dados em duas zonas de disponibilidade (se implantando em Azure) ou dois domínios de disponibilidade (em OCI). Utilize a Oracle Ative Data Guard para sincronizar as bases de dados primárias e de espera.

O nível de base de dados só recebe pedidos do nível médio. Recomenda-se que instale um grupo de segurança de rede (lista de segurança se implementar a base de dados em OCI) para permitir apenas pedidos na porta 1521 do nível médio e porta 22 do servidor do bastião por razões administrativas.

Para bases de dados implantadas no OCI, uma rede de nuvem virtual separada deve ser configurada com um gateway de encaminhamento dinâmico (DRG) que esteja ligado ao seu circuito FastConnect.