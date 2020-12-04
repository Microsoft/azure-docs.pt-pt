---
title: 'Capacidade de teste: Comunicação de serviço'
description: A comunicação serviço-a-serviço é um ponto crítico de integração de uma aplicação de Tecido de Serviço. Este artigo discute considerações de design e técnicas de teste.
ms.topic: conceptual
ms.date: 11/02/2017
ms.openlocfilehash: fbf889422d69d031d777acd80cf3becfb3184568
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575929"
---
# <a name="service-fabric-testability-scenarios-service-communication"></a>Cenários de testabilidade do tecido de serviço: comunicação de serviço
Microserviços e estilos arquitetônicos orientados para o serviço surgem naturalmente no Azure Service Fabric. Neste tipo de arquiteturas distribuídas, as aplicações de microserviços componentes são normalmente compostas por múltiplos serviços que precisam de falar entre si. Mesmo nos casos mais simples, você geralmente tem pelo menos um serviço web apátrida e um serviço de armazenamento de dados imponente que precisa de comunicar.

A comunicação serviço-a-serviço é um ponto crítico de integração de uma aplicação, porque cada serviço expõe uma API remota a outros serviços. Trabalhar com um conjunto de limites de API que envolve I/S geralmente requer algum cuidado, com uma boa quantidade de testes e validação.

Existem inúmeras considerações a fazer quando estes limites de serviço são ligados juntos num sistema distribuído:

* *Protocolo de transporte.* Utilizará HTTP para uma interoperabilidade acrescida ou um protocolo binário habitual para a produção máxima?
* *Manipulação de erros*. Como serão tratados erros permanentes e transitórios? O que acontecerá quando um serviço se mudar para um nó diferente?
* *Intervalos e latência.* Em aplicações multitiered, como é que cada camada de serviço lidará com a latência através da pilha e do utilizador?

Quer utilize um dos componentes de comunicação de serviço integrados fornecidos pela Service Fabric ou se constrói o seu próprio, testar as interações entre os seus serviços é fundamental para garantir resiliência na sua aplicação.

## <a name="prepare-for-services-to-move"></a>Preparar para os serviços para mover
As instâncias de serviço podem mover-se ao longo do tempo. Isto é especialmente verdade quando são configurados com métricas de carga para o equilíbrio de recursos ideal personalizado. O Service Fabric move as suas instâncias de serviço para maximizar a sua disponibilidade mesmo durante as atualizações, failovers, scale-out e outras situações que ocorrem ao longo da vida de um sistema distribuído.

À medida que os serviços se deslocam no cluster, os seus clientes e outros serviços devem estar preparados para lidar com dois cenários quando falam com um serviço:

* A instância de serviço ou réplica de partição moveu-se desde a última vez que falou com ela. Esta é uma parte normal de um ciclo de vida de serviço, e deve ser esperado que aconteça durante o tempo de vida da sua aplicação.
* A instância de serviço ou a réplica de partição está em processo de movimento. Embora o fracasso de um serviço de um nó para outro ocorra muito rapidamente no Tecido de Serviço, pode haver um atraso na disponibilidade se o componente de comunicação do seu serviço for lento para começar.

Lidar com estes cenários graciosamente é importante para um sistema de funcionamento suave. Para tal, lembre-se que:

* Todos os serviços que podem ser ligados têm um *endereço* que escuta (por exemplo, HTTP ou WebSockets). Quando uma instância de serviço ou partição se move, o seu ponto final de endereço muda. (Move-se para um nó diferente com um endereço IP diferente.) Se estiver a utilizar os componentes de comunicação incorporados, eles tratarão de reensar os endereços de serviço para si.
* Pode haver um aumento temporário da latência do serviço à medida que a instância de serviço recomeça o seu ouvinte. Isto depende da rapidez com que o serviço abre o ouvinte após a deslocação da instância de serviço.
* Quaisquer ligações existentes devem ser fechadas e reabertas após a abertura do serviço num novo nó. Uma paragem ou reinicialização gracioso do nó permite que as ligações existentes sejam desligadas graciosamente.

### <a name="test-it-move-service-instances"></a>Teste-o: Mover instâncias de serviço
Ao utilizar as ferramentas de testability do Service Fabric, pode ser autor de um cenário de teste para testar estas situações de diferentes formas:

1. Mover a réplica primária de um serviço imponente.
   
    A réplica primária de uma divisória de serviço imponente pode ser movida por várias razões. Use isto para direcionar a réplica primária de uma partição específica para ver como os seus serviços reagem ao movimento de uma forma muito controlada.
   
    ```powershell
   
    PS > Move-ServiceFabricPrimaryReplica -PartitionId 6faa4ffa-521a-44e9-8351-dfca0f7e0466 -ServiceName fabric:/MyApplication/MyService
   
    ```
2. Pare um nó.
   
    Quando um nó é parado, o Service Fabric move todas as instâncias de serviço ou divisórias que estavam nesse nó para um dos outros nós disponíveis no cluster. Utilize isto para testar uma situação em que um nó é perdido do seu cluster e todas as instâncias de serviço e réplicas nesse nó têm de se mover.
   
    Pode parar um nó utilizando o cmdlet PowerShell **Stop-ServiceFabricNode:**
   
    ```powershell
   
    PS > Stop-ServiceFabricNode -NodeName Node_1
   
    ```

## <a name="maintain-service-availability"></a>Manter a disponibilidade do serviço
Como plataforma, o Service Fabric foi concebido para fornecer alta disponibilidade dos seus serviços. Mas, em casos extremos, os problemas subjacentes às infraestruturas podem ainda causar indisponibilidade. É importante testar estes cenários também.

Os serviços estatais utilizam um sistema baseado em quórum para replicar o estado para uma elevada disponibilidade. Isto significa que um quórum de réplicas precisa estar disponível para realizar operações de escrita. Em casos raros, como uma falha de hardware generalizada, um quórum de réplicas pode não estar disponível. Nestes casos, não poderá realizar operações de escrita, mas ainda poderá realizar operações de leitura.

### <a name="test-it-write-operation-unavailability"></a>Teste:Escrever indisponibilidade da operação
Ao utilizar as ferramentas de testabilidade no Tecido de Serviço, pode injetar uma falha que induz a perda de quórum como teste. Embora tal cenário seja raro, é importante que os clientes e serviços que dependem de um serviço estatal estejam preparados para lidar com situações em que não possam fazer pedidos de escrita. É também importante que o próprio serviço estatal esteja ciente desta possibilidade e possa comunicá-la graciosamente aos chamadores.

Pode induzir a perda do quórum utilizando o cmdlet PowerShell **Invoke-ServiceFabricPartitionQuorumLoss:**

```powershell

PS > Invoke-ServiceFabricPartitionQuorumLoss -ServiceName fabric:/Myapplication/MyService -QuorumLossMode QuorumReplicas -QuorumLossDurationInSeconds 20

```

Neste exemplo, definimos `QuorumLossMode` `QuorumReplicas` para indicar que queremos induzir a perda de quórum sem derrubar todas as réplicas. Desta forma, as operações de leitura ainda são possíveis. Para testar um cenário em que uma partição inteira não esteja disponível, pode definir este interruptor para `AllReplicas` .

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre ações de testability](service-fabric-testability-actions.md)

[Saiba mais sobre cenários de testabilidade](service-fabric-testability-scenarios.md)

