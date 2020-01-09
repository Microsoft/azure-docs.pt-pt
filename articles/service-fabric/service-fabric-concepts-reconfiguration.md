---
title: Reconfiguração no Azure Service Fabric
description: Saiba mais sobre as configurações de réplicas de serviço com estado e o processo de reconfiguração Service Fabric usa para manter a consistência e a disponibilidade durante a alteração.
author: appi101
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: bd46a7776495624affef77a44fcf68334750ba17
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610000"
---
# <a name="reconfiguration-in-azure-service-fabric"></a>Reconfiguração no Azure Service Fabric
Uma *configuração* é definida como réplicas e suas funções para uma partição de um serviço com estado.

Uma *reconfiguração* é o processo de mover uma configuração para outra configuração. Ele faz uma alteração no conjunto de réplicas para uma partição de um serviço com estado. A configuração antiga é chamada de *PC (configuração anterior)* , e a nova configuração é chamada de *configuração atual (CC)* . O protocolo de reconfiguração no Azure Service Fabric preserva a consistência e mantém a disponibilidade durante qualquer alteração no conjunto de réplicas.

Gerenciador de Failover inicia as reconfigurações em resposta a eventos diferentes no sistema. Por exemplo, se o primário falhar, uma reconfiguração será iniciada para promover um secundário ativo para um primário. Outro exemplo é em resposta a atualizações de aplicativo quando pode ser necessário mover o primário para outro nó a fim de atualizar o nó.

## <a name="reconfiguration-types"></a>Tipos de reconfiguração
As reconfigurações podem ser classificadas em dois tipos:

- Reconfigurações em que o primário está sendo alterado:
    - **Failover**: failovers são reconfigurações em resposta à falha de um primário em execução.
    - **SwapPrimary**: as trocas são reconfigurações em que Service Fabric precisa mover um primário em execução de um nó para outro, geralmente em resposta ao balanceamento de carga ou a uma atualização.

- Reconfigurações em que a primária não está sendo alterada.

## <a name="reconfiguration-phases"></a>Fases de reconfiguração
Uma reconfiguração prossegue em várias fases:

- **Phase0**: essa fase ocorre em reconfigurações de troca primária em que o primário atual transfere seu estado para o novo primário e faz a transição para o secundário ativo.

- **Fase1**: essa fase ocorre durante as reconfigurações em que o primário está sendo alterado. Durante essa fase, Service Fabric identifica a primária correta entre as réplicas atuais. Essa fase não é necessária durante as reconfigurações de troca primária porque a nova primária já foi escolhida. 

- A **Fase2**: durante essa fase, Service Fabric garante que todos os dados estejam disponíveis na maioria das réplicas da configuração atual.

Há várias outras fases que são somente para uso interno.

## <a name="stuck-reconfigurations"></a>Reconfigurações presas
As reconfigurações podem ficar *presas* por vários motivos. Alguns dos motivos comuns incluem:

- **Réplicas inativas**: algumas fases de reconfiguração exigem a maioria das réplicas na configuração a ser ativada.
- **Problemas de rede ou comunicação**: as reconfigurações exigem conectividade de rede entre nós diferentes.
- **Falhas de API**: o protocolo de reconfiguração requer que as implementações de serviço concluam determinadas APIs. Por exemplo, não honrar o token de cancelamento em um serviço confiável faz com que as reconfigurações do SwapPrimary fiquem presas.

Use relatórios de integridade de componentes do sistema, como System.FM, System. RA e System. RAP, para diagnosticar onde uma reconfiguração está paralisada. A [página do relatório de integridade do sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) descreve esses relatórios de integridade.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre conceitos de Service Fabric, consulte os seguintes artigos:

- [Ciclo de vida do Reliable Services - C#](service-fabric-reliable-services-lifecycle.md)
- [Relatórios de integridade do sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Réplicas e instâncias](service-fabric-concepts-replica-lifecycle.md)
