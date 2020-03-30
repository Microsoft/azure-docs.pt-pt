---
title: Reconfiguração em Tecido de Serviço Azure
description: Aprenda sobre configurações para réplicas de serviço sinuosos e o processo de reconfiguração do serviço de tecido usa para manter consistência e disponibilidade durante a mudança.
author: appi101
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: bd46a7776495624affef77a44fcf68334750ba17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610000"
---
# <a name="reconfiguration-in-azure-service-fabric"></a>Reconfiguração em Tecido de Serviço Azure
Uma *configuração* é definida como as réplicas e seus papéis para uma partição de um serviço imponente.

Uma *reconfiguração* é o processo de mover uma configuração para outra configuração. Faz uma alteração na réplica definida para uma partição de um serviço imponente. A configuração antiga chama-se *configuração anterior (PC)* e a nova configuração chama-se *configuração atual (CC)*. O protocolo de reconfiguração em Tecido de Serviço Azure preserva a consistência e mantém a disponibilidade durante quaisquer alterações ao conjunto de réplicas.

O Failover Manager inicia reconfigurações em resposta a diferentes eventos no sistema. Por exemplo, se a primária falhar, então é iniciada uma reconfiguração para promover um secundário ativo para uma primária. Outro exemplo é em resposta a atualizações de aplicações quando pode ser necessário mover o primário para outro nó para atualizar o nó.

## <a name="reconfiguration-types"></a>Tipos de reconfiguração
As reconfigurações podem ser classificadas em dois tipos:

- Reconfigurações onde as primárias estão a mudar:
    - **Failover**: As falhas são reconfigurações em resposta à falha de uma primária em execução.
    - **SwapPrimary**: As trocas são reconfigurações em que o Tecido de Serviço precisa de mover uma primária em funcionamento de um nó para outro, geralmente em resposta ao equilíbrio de carga ou a uma atualização.

- Reconfigurações onde as primárias não estão a mudar.

## <a name="reconfiguration-phases"></a>Fases de reconfiguração
Uma reconfiguração prossegue em várias fases:

- **Fase0a**: Esta fase acontece em reconfigurações primárias de swap onde o atual primário transfere o seu estado para o novo primário e transições para o secundário ativo.

- **Fase 1**: Esta fase acontece durante as reconfigurações em que o primário está a mudar. Durante esta fase, o Tecido de Serviço identifica as primárias corretas entre as réplicas atuais. Esta fase não é necessária durante as reconfigurações primárias de swap porque a nova primária já foi escolhida. 

- **Fase 2**: Durante esta fase, o Tecido de Serviço garante que todos os dados estão disponíveis na maioria das réplicas da configuração atual.

Existem várias outras fases que são apenas para uso interno.

## <a name="stuck-reconfigurations"></a>Reconfigurações presas
As reconfigurações podem ficar *presas* por uma variedade de razões. Algumas das razões comuns incluem:

- **Réplicas para baixo**: Algumas fases de reconfiguração requerem que a maioria das réplicas na configuração seja para cima.
- **Problemas de rede ou comunicação**: As reconfigurações requerem conectividade da rede entre diferentes nós.
- Falhas da **API**: O protocolo de reconfiguração requer que as implementações de serviços terminem certas APIs. Por exemplo, não honrar o símbolo de cancelamento num serviço fiável faz com que as reconfigurações SwapPrimary fique presa.

Utilize relatórios de saúde de componentes do sistema, tais como System.FM, System.RA e System.RAP, para diagnosticar onde uma reconfiguração está presa. A página do relatório de [saúde](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) do sistema descreve estes relatórios de saúde.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre os conceitos de Tecido de Serviço, consulte os seguintes artigos:

- [Ciclo de vida do Reliable Services - C#](service-fabric-reliable-services-lifecycle.md)
- [Relatórios de saúde do sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Réplicas e instâncias](service-fabric-concepts-replica-lifecycle.md)
