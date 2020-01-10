---
title: 'Capacidade de teste: comunicação de serviço'
description: A comunicação entre serviços é um ponto de integração crítico de um aplicativo Service Fabric. Este artigo discute as considerações de design e técnicas de teste.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 87b922cb9655588a22c739d26c9ce9e49d35781a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75465563"
---
# <a name="service-fabric-testability-scenarios-service-communication"></a>Cenários de possibilidade de teste Service Fabric: comunicação de serviço
Os microserviços e a superfície de estilos de arquitetura orientada a serviços naturalmente no Azure Service Fabric. Nesses tipos de arquiteturas distribuídas, aplicativos de microatendimento com componentes são normalmente compostos por vários serviços que precisam se comunicar entre si. Até os casos mais simples, geralmente você tem pelo menos um serviço Web sem estado e um serviço de armazenamento de dados com estado que precisam se comunicar.

A comunicação entre serviços é um ponto de integração crítico de um aplicativo, pois cada serviço expõe uma API remota a outros serviços. Trabalhar com um conjunto de limites de API que envolvem e/s geralmente requer algum cuidado, com uma boa quantidade de testes e validação.

Há várias considerações a serem feitas quando esses limites de serviço são vinculados em um sistema distribuído:

* *Protocolo de transporte*. Você usará HTTP para maior interoperabilidade ou um protocolo binário personalizado para taxa de transferência máxima?
* *Tratamento de erros*. Como os erros permanentes e transitórios serão tratados? O que acontecerá quando um serviço for movido para um nó diferente?
* *Tempos limite e latência*. Em aplicativos multicamadas, como cada camada de serviço lida com a latência por meio da pilha e para o usuário?

Se você usar um dos componentes de comunicação de serviço internos fornecidos pelo Service Fabric ou se você criar seu próprio, testar as interações entre seus serviços é essencial para garantir a resiliência em seu aplicativo.

## <a name="prepare-for-services-to-move"></a>Preparar para mover os serviços
As instâncias de serviço podem se movimentar ao longo do tempo. Isso é especialmente verdadeiro quando eles são configurados com métricas de carga para balanceamento de recursos ideal personalizado. Service Fabric move suas instâncias de serviço para maximizar sua disponibilidade mesmo durante atualizações, failovers, expansão e outras situações que ocorrem durante o tempo de vida de um sistema distribuído.

À medida que os serviços se movimentam no cluster, seus clientes e outros serviços devem estar preparados para lidar com dois cenários quando se comunicam com um serviço:

* A instância de serviço ou a réplica de partição foi movida desde a última vez que você falou. Essa é uma parte normal de um ciclo de vida do serviço e deve ocorrer durante o tempo de vida do seu aplicativo.
* A instância de serviço ou a réplica de partição está no processo de movimentação. Embora o failover de um serviço de um nó para outro ocorra muito rapidamente no Service Fabric, pode haver um atraso na disponibilidade se o componente de comunicação do serviço estiver lento para ser iniciado.

Tratar esses cenários normalmente é importante para um sistema de execução tranqüila. Para fazer isso, tenha em mente que:

* Cada serviço que pode ser conectado tem um *endereço* em que ele escuta (por exemplo, http ou WebSockets). Quando uma instância ou partição de serviço é movida, seu ponto de extremidade de endereço é alterado. (Ele se move para um nó diferente com um endereço IP diferente.) Se você estiver usando os componentes de comunicação internos, eles tratarão de reresolver os endereços de serviço para você.
* Pode haver um aumento temporário na latência do serviço, pois a instância do serviço inicia seu ouvinte novamente. Isso depende de quão rápido o serviço abre o ouvinte depois que a instância do serviço é movida.
* Todas as conexões existentes precisam ser fechadas e reabertas depois que o serviço é aberto em um novo nó. Um desligamento ou reinício de nó normal permite que as conexões existentes sejam desligadas normalmente.

### <a name="test-it-move-service-instances"></a>Teste: mover instâncias de serviço
Usando as ferramentas de teste do Service Fabric, você pode criar um cenário de teste para testar essas situações de maneiras diferentes:

1. Mova uma réplica primária de um serviço com estado.
   
    A réplica primária de uma partição de serviço com estado pode ser movida por vários motivos. Use isso para direcionar a réplica primária de uma partição específica para ver como seus serviços reagem para a movimentação de uma maneira muito controlada.
   
    ```powershell
   
    PS > Move-ServiceFabricPrimaryReplica -PartitionId 6faa4ffa-521a-44e9-8351-dfca0f7e0466 -ServiceName fabric:/MyApplication/MyService
   
    ```
2. Parar um nó.
   
    Quando um nó é interrompido, Service Fabric move todas as instâncias de serviço ou partições que estavam nesse nó para um dos outros nós disponíveis no cluster. Use isso para testar uma situação em que um nó é perdido do seu cluster e todas as instâncias de serviço e réplicas nesse nó precisam ser movidas.
   
    Você pode interromper um nó usando o cmdlet **Stop-ServiceFabricNode** do PowerShell:
   
    ```powershell
   
    PS > Stop-ServiceFabricNode -NodeName Node_1
   
    ```

## <a name="maintain-service-availability"></a>Manter a disponibilidade do serviço
Como plataforma, a Service Fabric foi projetada para fornecer alta disponibilidade de seus serviços. Mas, em casos extremos, problemas de infraestrutura subjacentes ainda podem causar indisponibilidade. É importante testar esses cenários também.

Os serviços com estado usam um sistema baseado em quorum para replicar o estado para alta disponibilidade. Isso significa que um quorum de réplicas precisa estar disponível para executar operações de gravação. Em casos raros, como uma falha de hardware generalizada, um quorum de réplicas pode não estar disponível. Nesses casos, você não poderá executar operações de gravação, mas ainda poderá executar operações de leitura.

### <a name="test-it-write-operation-unavailability"></a>Teste: indisponibilidade de operação de gravação
Usando as ferramentas de possibilidade de teste no Service Fabric, você pode injetar uma falha que induz a perda de quorum como um teste. Embora esse cenário seja raro, é importante que os clientes e serviços que dependem de um serviço com estado estejam preparados para lidar com situações em que não podem fazer solicitações de gravação para ele. Também é importante que o serviço com estado em si esteja ciente dessa possibilidade e possa comunicar-se normalmente com os chamadores.

Você pode induzir a perda de quorum usando o cmdlet **Invoke-ServiceFabricPartitionQuorumLoss** do PowerShell:

```powershell

PS > Invoke-ServiceFabricPartitionQuorumLoss -ServiceName fabric:/Myapplication/MyService -QuorumLossMode QuorumReplicas -QuorumLossDurationInSeconds 20

```

Neste exemplo, definimos `QuorumLossMode` como `QuorumReplicas` para indicar que queremos induzir a perda de quorum sem desativar todas as réplicas. Dessa forma, as operações de leitura ainda são possíveis. Para testar um cenário em que uma partição inteira não está disponível, você pode definir essa opção como `AllReplicas`.

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre as ações de possibilidade de teste](service-fabric-testability-actions.md)

[Saiba mais sobre cenários de possibilidade de teste](service-fabric-testability-scenarios.md)

