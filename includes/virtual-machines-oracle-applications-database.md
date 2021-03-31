---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 06/01/2020
ms.author: danlep
ms.openlocfilehash: b9e275cbdaf264df3d0a40f032693fd93553e520
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "84317488"
---
### <a name="database-tier"></a>Nível de base de dados

O nível de base de dados contém as instâncias da base de dados para a aplicação. A base de dados pode ser um sistema oracle DB, Oracle RAC ou Oracle Exadata Database. 

Se a escolha for utilizar o Oracle DB, a caixa de dados pode ser implantada no Azure através das imagens Oráculo DB disponíveis no Azure Marketplace. Em alternativa, pode utilizar a interligação entre a Azure e o OCI para implantar o Oráculo DB num modelo PaaS no OCI.

Para o Oracle RAC, pode utilizar o OCI no modelo PaaS. Recomenda-se que utilize um sistema RAC de dois nós. Embora seja possível implantar o Oracle RAC no Azure CloudSimple no modelo IaaS, não é uma configuração suportada pela Oracle. Consulte os [programas da Oracle elegíveis para Ambientes de Nuvem Autorizados](http://www.oracle.com/us/corporate/pricing/authorized-cloud-environments-3493562.pdf).

Finalmente, para os sistemas Exadata, utilize a interligação OCI e implante o sistema Exadata no OCI. O diagrama de arquitetura anterior acima mostra um sistema Exadata implantado em OCI em duas sub-redes.

Para cenários de produção, implementar várias instâncias da base de dados em duas zonas de disponibilidade (se implantar em Azure) ou dois domínios de disponibilidade (em OCI). Utilize a Oracle Ative Data Guard para sincronizar as bases de dados primárias e de espera.

O nível de base de dados só recebe pedidos do nível médio. Recomenda-se que crie um grupo de segurança de rede (lista de segurança se implementar a base de dados no OCI) para permitir apenas pedidos na porta 1521 a partir do nível médio e porta 22 do servidor de bastião por razões administrativas.

Para as bases de dados implementadas no OCI, deve ser criada uma rede de nuvem virtual separada com um gateway de encaminhamento dinâmico (DRG) que esteja ligado ao seu circuito FastConnect.