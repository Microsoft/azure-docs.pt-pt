---
title: 'Capacidade de teste: Comunicação de serviço | Documentos da Microsoft'
description: Comunicação de serviço a serviço é um ponto de integração críticas de uma aplicação do Service Fabric. Este artigo aborda considerações de design e técnicas de teste.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 017557df-fb59-4e4a-a65d-2732f29255b8
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 529c8d74b6e0a63a7969f31d5b5e8073ecb79411
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60543228"
---
# <a name="service-fabric-testability-scenarios-service-communication"></a>Cenários de capacidade de teste do Service Fabric: Comunicação do serviço
Microsserviços e a superfície de estilos de arquitetura orientada a serviços naturalmente no Azure Service Fabric. Estes tipos de arquiteturas distribuídas, aplicações de microsserviços dividida em componentes são normalmente compostas por vários serviços que têm de conversar entre si. Até mesmo os casos mais simples, geralmente, é ter, pelo menos, um serviço web sem monitorização de estado e um serviço de armazenamento de dados com monitoração de estado que precisam se comunicar.

Comunicação de serviço a serviço é um ponto de integração críticas de uma aplicação, uma vez que cada serviço expõe uma API remota a outros serviços. Trabalhar com um conjunto de limites de API que envolve a e/s geralmente exige um certo cuidado, com uma boa quantidade de teste e validação.

Existem várias considerações a tomar quando estes limites de serviço são conectados num sistema distribuído:

* *Protocolo de transporte*. Irá utilizar HTTP para o aumento da interoperabilidade ou de um protocolo binário personalizado para um débito máximo?
* *Tratamento de erros*. Como serão tratados erros transitórios e permanentes? O que acontece quando um serviço é movido para um nó diferente?
* *Tempos limite e latência*. Em aplicativos de várias camadas, como será cada camada de serviço com latência através da pilha de e para o utilizador?

Se usar um dos componentes de comunicação de serviço interno fornecidos pelo Service Fabric ou criar seus próprios, as interações entre os serviços de testes é fundamental para garantir a resiliência em seu aplicativo.

## <a name="prepare-for-services-to-move"></a>Preparar para os serviços mover
Podem mover instâncias do serviço ao longo do tempo. Isso é especialmente verdadeiro quando são configurados com a métrica de carga para o balanceamento de recurso de ideal personalizadas. Service Fabric move as instâncias de serviço para maximizar a sua disponibilidade mesmo durante atualizações, as ativações pós-falha, escalável e outras situações que ocorrem durante o ciclo de vida de um sistema distribuído.

Como serviços movem-se no cluster, os seus clientes e outros serviços devem estar preparados para lidar com dois cenários ao falar para um serviço:

* A réplica de instância ou partição de serviço foi movido desde a última vez que falou a ele. Esta é uma parte normal de um ciclo de vida do serviço e deve acontecer durante a vida útil do seu aplicativo.
* A réplica de instância ou partição de serviço está em vias de mudar. Apesar de ativação pós-falha de um serviço de um nó para outro ocorre muito rapidamente nos recursos de infraestrutura do serviço, poderá ocorrer um atraso na disponibilidade se o componente de comunicação do seu serviço estiver lento iniciar.

Lidar com esses cenários normalmente é importante para um sistema com execução uniforme. Para fazer isso, tenha em atenção que:

* Tem de todos os serviços que podem ser ligados a uma *endereço* que fica à escuta em (por exemplo, HTTP ou WebSockets). Quando uma instância de serviço ou a partição for movido, altera o seu ponto de extremidade de endereço. (Ele passa para um nó diferente com um endereço IP diferente.) Se estiver a utilizar os componentes de comunicação incorporada, eles irão processar novamente resolver endereços de serviço para.
* Talvez haja um aumento temporário do número de latência do serviço como a instância de serviço é iniciada a escuta novamente. Isso depende de como rapidamente o serviço abre o serviço de escuta depois de mover a instância do serviço.
* Todas as ligações existentes tem de ser fechado e reaberto depois do serviço abre num novo nó. Um encerramento de nó anulações normais ou reinício dá tempo para que as ligações existentes ao encerrar corretamente.

### <a name="test-it-move-service-instances"></a>Testá-lo: Mover instâncias do serviço
Ao utilizar ferramentas de capacidade de teste do Service Fabric, pode criar um cenário de teste para testar essas situações de formas diferentes:

1. Mova a réplica primária de um serviço com estado.
   
    A réplica primária de uma partição de serviço com estado pode ser movida por diversas razões. Utilize esta opção para a réplica primária de uma partição específica para ver como os serviços de reagem a mudança de forma muito controlada de destino.
   
    ```powershell
   
    PS > Move-ServiceFabricPrimaryReplica -PartitionId 6faa4ffa-521a-44e9-8351-dfca0f7e0466 -ServiceName fabric:/MyApplication/MyService
   
    ```
2. Pare um nó.
   
    Quando um nó estiver parado, o Service Fabric move todas as instâncias de serviço ou partições que estavam nesse nó a um dos outros nós disponíveis no cluster. Utilize esta opção para testar uma situação em que um nó é perdido do seu cluster e todas as instâncias de serviço e réplicas nesse nó tem de mover.
   
    Pode parar um nó através do PowerShell **Stop-ServiceFabricNode** cmdlet:
   
    ```powershell
   
    PS > Stop-ServiceFabricNode -NodeName Node_1
   
    ```

## <a name="maintain-service-availability"></a>Manter a disponibilidade de serviço
Como uma plataforma, o Service Fabric foi concebido para proporcionar elevada disponibilidade dos seus serviços. Mas, em casos extremos, problemas de infraestrutura subjacentes podem causar indisponibilidade. É importante testar nestes cenários, também.

Serviços com estado utilizam um sistema baseado em quórum para replicar o estado de elevada disponibilidade. Isso significa que um quórum de réplicas tem de estar disponíveis para realizar operações de escrita. Em casos raros, tais como uma falha de hardware ampla, um quórum de réplicas pode não estar disponível. Nestes casos, não será capaz de executar operações de escrita, mas continuará a ser capaz de executar operações de leitura.

### <a name="test-it-write-operation-unavailability"></a>Testá-lo: Indisponibilidade de operação de escrita
Ao utilizar as ferramentas de capacidade de teste no Service Fabric, pode injetar uma falha que induces perda de quórum como um teste. Embora o cenário é raro, é importante que os clientes e serviços que dependem de um serviço com estado estão preparados para lidar com situações em que eles não é possível efetuar pedidos de escrita ao mesmo. Também é importante que o serviço com estado em si está ciente dessa possibilidade e corretamente a pode comunicar com os autores de chamadas.

Podem induzi-las a perda de quórum ao utilizar o PowerShell **Invoke-ServiceFabricPartitionQuorumLoss** cmdlet:

```powershell

PS > Invoke-ServiceFabricPartitionQuorumLoss -ServiceName fabric:/Myapplication/MyService -QuorumLossMode QuorumReplicas -QuorumLossDurationInSeconds 20

```

Neste exemplo, definimos `QuorumLossMode` para `QuorumReplicas` para indicar que queremos induza perda de quórum sem ter de encerrar todas as réplicas. Dessa forma, operações de leitura são ainda possíveis. Para testar um cenário em que uma partição inteira está indisponível, pode definir esse comutador `AllReplicas`.

## <a name="next-steps"></a>Passos Seguintes
[Saiba mais sobre as ações de capacidade de teste](service-fabric-testability-actions.md)

[Saiba mais sobre cenários do testability](service-fabric-testability-scenarios.md)

