---
title: 'Testabilidade: Comunicação de serviço'
description: A comunicação serviço-a-serviço é um ponto crítico de integração de uma aplicação Service Fabric. Este artigo discute considerações de design e técnicas de teste.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 87b922cb9655588a22c739d26c9ce9e49d35781a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75465563"
---
# <a name="service-fabric-testability-scenarios-service-communication"></a>Cenários de testabilidade do tecido de serviço: Comunicação de serviço
Microserviços e estilos arquitetónicos orientados para o serviço surgem naturalmente no Tecido de Serviço Azure. Neste tipo de arquiteturas distribuídas, as aplicações de microserviços componentes são tipicamente compostas por múltiplos serviços que precisam de falar uns com os outros. Mesmo nos casos mais simples, você geralmente tem pelo menos um serviço web apátrida e um serviço de armazenamento de dados apátrida que precisa comunicar.

A comunicação serviço-a-serviço é um ponto crítico de integração de uma aplicação, porque cada serviço expõe uma API remota a outros serviços. Trabalhar com um conjunto de limites de API que envolve I/O geralmente requer algum cuidado, com uma boa quantidade de testes e validação.

Existem numerosas considerações a fazer quando estes limites de serviço são ligados num sistema distribuído:

* *Protocolo de transporte.* Utilizará http para uma interoperabilidade acrescida, ou um protocolo binário personalizado para a máxima potência?
* *Manipulação de erros*. Como serão tratados erros permanentes e transitórios? O que vai acontecer quando um serviço passar para um nó diferente?
* *Intervalos e latência.* Em aplicações multicamadas, como é que cada camada de serviço manterá a latência através da pilha e do utilizador?

Quer utilize um dos componentes de comunicação de serviço incorporados fornecidos pela Service Fabric ou se constrói o seu próprio, testar as interações entre os seus serviços é fundamental para garantir a resiliência na sua aplicação.

## <a name="prepare-for-services-to-move"></a>Prepare-se para os serviços se moverem
As instâncias de serviço podem mover-se ao longo do tempo. Isto é especialmente verdade quando são configurados com métricas de carga para um equilíbrio de recursos ideal personalizado. O Service Fabric move as suas instâncias de serviço para maximizar a sua disponibilidade mesmo durante upgrades, failovers, scale-out e outras situações que ocorrem ao longo da vida de um sistema distribuído.

À medida que os serviços se movem no cluster, os seus clientes e outros serviços devem estar preparados para lidar com dois cenários quando falam com um serviço:

* A instância de serviço ou réplica de divisória moveu-se desde a última vez que falou com ela. Esta é uma parte normal de um ciclo de vida de serviço, e deve acontecer durante a vida da sua aplicação.
* A instância de serviço ou réplica da divisória está em processo de deslocação. Embora a falha de um serviço de um nó para outro ocorra muito rapidamente no Tecido de Serviço, pode haver um atraso na disponibilidade se o componente de comunicação do seu serviço for lento para iniciar.

Lidar com estes cenários graciosamente é importante para um sistema de funcionamento suave. Para fazê-lo, lembre-se que:

* Cada serviço que pode ser ligado tem um *endereço* que ouve (por exemplo, HTTP ou WebSockets). Quando uma instância de serviço ou partição se move, o seu ponto final de endereço muda. (Muda-se para um nó diferente com um endereço IP diferente.) Se estiver a utilizar os componentes de comunicação incorporados, tratarão da reresolução de endereços de serviço para si.
* Pode haver um aumento temporário da latência do serviço à medida que a instância de serviço recomeça o seu ouvinte. Isto depende da rapidez com que o serviço abre o ouvinte após a deslocação da instância de serviço.
* Quaisquer ligações existentes precisam de ser fechadas e reabertas após a abertura do serviço num novo nó. Uma paragem ou reinício gracioso do nó permite que as ligações existentes sejam desligadas graciosamente.

### <a name="test-it-move-service-instances"></a>Teste-o: Mover instâncias de serviço
Utilizando as ferramentas de testabilidade do Service Fabric, pode ser autor de um cenário de teste para testar estas situações de diferentes formas:

1. Mova a réplica primária de um serviço imponente.
   
    A réplica primária de uma partição de serviço estatal pode ser movida por várias razões. Use isto para direcionar a réplica primária de uma divisória específica para ver como os seus serviços reagem ao movimento de forma muito controlada.
   
    ```powershell
   
    PS > Move-ServiceFabricPrimaryReplica -PartitionId 6faa4ffa-521a-44e9-8351-dfca0f7e0466 -ServiceName fabric:/MyApplication/MyService
   
    ```
2. Pare um nó.
   
    Quando um nó é parado, o Service Fabric move todas as instâncias de serviço ou divisórias que estavam nesse nó para um dos outros nós disponíveis no cluster. Use isto para testar uma situação em que um nó é perdido do seu cluster e todas as instâncias de serviço e réplicas desse nó têm que se mover.
   
    Pode parar um nó utilizando o cmdlet PowerShell **Stop-ServiceFabricNode:**
   
    ```powershell
   
    PS > Stop-ServiceFabricNode -NodeName Node_1
   
    ```

## <a name="maintain-service-availability"></a>Manter a disponibilidade do serviço
Como plataforma, o Service Fabric foi projetado para fornecer uma elevada disponibilidade dos seus serviços. Mas, em casos extremos, os problemas subjacentes às infraestruturas podem ainda causar indisponibilidade. É importante testar também estes cenários.

Os serviços estatais usam um sistema baseado em quórum para replicar o estado para uma elevada disponibilidade. Isto significa que um quórum de réplicas precisa estar disponível para realizar operações de escrita. Em casos raros, como uma falha generalizada no hardware, um quórum de réplicas pode não estar disponível. Nestes casos, não poderá realizar operações de escrita, mas ainda poderá realizar operações de leitura.

### <a name="test-it-write-operation-unavailability"></a>Teste-o: Escrever indisponibilidade de operação
Ao utilizar as ferramentas de testabilidade no Tecido de Serviço, pode injetar uma falha que induz a perda de quórum como teste. Embora tal cenário seja raro, é importante que os clientes e serviços que dependem de um serviço de estado estejam preparados para lidar com situações em que não podem fazer pedidos de escrita. Também é importante que o próprio serviço esteja ciente desta possibilidade e possa comunicá-lo graciosamente aos chamadores.

Pode induzir a perda de quórum utilizando o PowerShell **Invoke-ServicePartitionPartitionQuorumLoss** cmdlet:

```powershell

PS > Invoke-ServiceFabricPartitionQuorumLoss -ServiceName fabric:/Myapplication/MyService -QuorumLossMode QuorumReplicas -QuorumLossDurationInSeconds 20

```

Neste exemplo, `QuorumLossMode` definimos `QuorumReplicas` para indicar que queremos induzir a perda de quórum sem derrubar todas as réplicas. Desta forma, as operações de leitura ainda são possíveis. Para testar um cenário em que uma partição `AllReplicas`inteira não esteja disponível, pode definir este interruptor para .

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre ações de testability](service-fabric-testability-actions.md)

[Saiba mais sobre cenários de testability](service-fabric-testability-scenarios.md)

