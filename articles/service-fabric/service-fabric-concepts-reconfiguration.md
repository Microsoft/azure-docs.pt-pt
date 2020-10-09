---
title: Reconfiguração em Tecido de Serviço Azure
description: Saiba mais sobre configurações para réplicas de serviços imponentes e o processo de reconfiguração O Service Fabric utiliza para manter a consistência e disponibilidade durante a mudança.
author: appi101
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: bd46a7776495624affef77a44fcf68334750ba17
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75610000"
---
# <a name="reconfiguration-in-azure-service-fabric"></a>Reconfiguração em Tecido de Serviço Azure
Uma *configuração* é definida como as réplicas e os seus papéis para uma divisão de um serviço stateful.

Uma *reconfiguração* é o processo de mover uma configuração para outra configuração. Faz uma alteração na réplica definida para uma divisão de um serviço imponente. A configuração antiga é chamada de *configuração anterior (PC)*, e a nova configuração é chamada de *configuração atual (CC)*. O protocolo de reconfiguração no Azure Service Fabric preserva a consistência e mantém a disponibilidade durante quaisquer alterações ao conjunto de réplicas.

O Failover Manager inicia reconfigurações em resposta a diferentes eventos no sistema. Por exemplo, se o primário falhar, então uma reconfiguração é iniciada para promover um secundário ativo para um primário. Outro exemplo é a resposta às atualizações de aplicações quando pode ser necessário mover o primário para outro nó para atualizar o nó.

## <a name="reconfiguration-types"></a>Tipos de reconfiguração
As reconfigurações podem ser classificadas em dois tipos:

- Reconfigurações onde o primário está a mudar:
    - **Failover**: As falhas são reconfigurações em resposta à falha de uma primária em execução.
    - **SwapPrimary**: As trocas são reconfigurações em que o Service Fabric precisa de mover uma primária em execução de um nó para outro, normalmente em resposta ao equilíbrio de carga ou a uma atualização.

- Reconfigurações onde o primário não está a mudar.

## <a name="reconfiguration-phases"></a>Fases de reconfiguração
Uma reconfiguração prossegue em várias fases:

- **Fase 0**: Esta fase acontece em reconfigurações primárias de swap, onde a corrente primária transfere o seu estado para o novo primário e as transições para o secundário ativo.

- **Fase1**: Esta fase acontece durante as reconfigurações em que o primário está a mudar. Durante esta fase, o Tecido de Serviço identifica o primário correto entre as réplicas atuais. Esta fase não é necessária durante as reconfigurações primárias de swap porque o novo primário já foi escolhido. 

- **Fase2**: Durante esta fase, o Service Fabric garante que todos os dados estão disponíveis na maioria das réplicas da configuração atual.

Há várias outras fases que são apenas para uso interno.

## <a name="stuck-reconfigurations"></a>Reconfigurações presas
As reconfigurações podem ficar *presas* por uma variedade de razões. Algumas das razões comuns incluem:

- **Réplicas para baixo**: Algumas fases de reconfiguração requerem que a maioria das réplicas na configuração estejam a subir.
- **Problemas de rede ou de comunicação**: As reconfigurações requerem conectividade de rede entre diferentes nós.
- **Falhas da API**: O protocolo de reconfiguração requer que as implementações do serviço terminem determinadas APIs. Por exemplo, não honrar o token de cancelamento num serviço fiável faz com que as reconfigurações Do SwapPrimary fique presa.

Utilize relatórios de saúde de componentes do sistema, tais como System.FM, System.RA e System.RAP, para diagnosticar onde uma reconfiguração está presa. A [página do relatório de saúde do sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) descreve estes relatórios de saúde.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre os conceitos de Tecido de Serviço, consulte os seguintes artigos:

- [Ciclo de vida do Reliable Services - C#](service-fabric-reliable-services-lifecycle.md)
- [Relatórios de saúde do sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Réplicas e instâncias](service-fabric-concepts-replica-lifecycle.md)
