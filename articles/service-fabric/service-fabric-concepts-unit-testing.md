---
title: Teste de unidade serviços com estado no Azure Service Fabric | Documentos da Microsoft
description: Saiba mais sobre os conceitos e práticas de serviços do Service Fabric com monitorização de estado de teste de unidade.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/04/2018
ms.author: atsenthi
ms.openlocfilehash: ca473b9947a9b0df610a9c3dac66914b06cc9217
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60881458"
---
# <a name="unit-testing-stateful-services-in-service-fabric"></a>Teste de unidade serviços com estado no Service Fabric

Este artigo abrange os conceitos e práticas de serviços do Service Fabric com monitorização de estado de teste de unidade. Teste dentro do Service Fabric de unidade merecida suas próprias considerações que ativamente o código do aplicativo é executado em vários contextos diferentes. Este artigo descreve práticas utilizadas para garantir que o código do aplicativo é abordado em cada um dos contextos diferentes, que pode ser executada.

## <a name="unit-testing-and-mocking"></a>Teste de unidade e simulação
Teste de unidade no contexto deste artigo é automatizada de teste que podem ser executadas no contexto de um executor de teste, como o MSTest ou NUnit. Os testes de unidade neste artigo não execução de operações num recurso remoto como uma base de dados ou uma RESTFul API. Esses recursos remotos devem ser simulados. Simulação no contexto deste artigo será enganar, registo e controlar os valores de retorno para recursos remotos.

### <a name="service-fabric-considerations"></a>Considerações sobre o Service Fabric
Um serviço com monitorização de estado do Service Fabric de teste de unidade possui várias considerações. Em primeiro lugar, o código de serviço é executado em vários nós, mas em funções diferentes. Testes de unidade devem avaliar o código em cada função para atingir a cobertura completa. As diferentes funções seria primária, secundária ativa, secundária inativa e desconhecido. A nenhuma função geralmente não precisa qualquer cobertura especial como o Service Fabric considera esta função para ser serviço void ou nulo. Em segundo lugar, cada nó irá alterar a sua função num determinado período. Para atingir a cobertura completa, caminho de execução de código deve ser testado com alterações de função que ocorrem.

## <a name="why-unit-test-stateful-services"></a>Por que motivo de teste de unidade serviços com estado? 
Serviços com estado de teste de unidade pode ajudar a descobrir alguns erros comuns que são feitos o que seriam não necessariamente detetados por aplicativo convencional ou teste de unidade específica do domínio. Por exemplo, se o serviço com estado tiver qualquer estado dentro da memória, esse tipo de teste pode verificar que neste estado dentro da memória é mantido em sincronia entre cada réplica. Esse tipo de teste também pode verificar que um serviço com monitorização de estado responde a cancelamento dos tokens transmitido pela orquestração do Service Fabric adequadamente. Quando são acionados cancelamentos, o serviço deve parar quaisquer operações de longa execução e/ou assíncronas.  

## <a name="common-practices"></a>Práticas comuns

A secção seguinte aconselha-se nas práticas mais comuns para um serviço com estado de teste de unidade. Também informa sobre o que deve ter uma camada de simulação para alinhada com a orquestração do Service Fabric e o gerenciamento de estado. Bibliotecas de simulação existem bibliotecas que fornecem essa funcionalidade. [ServiceFabric.Mocks](https://www.nuget.org/packages/ServiceFabric.Mocks/) no momento da elaboração 3.3.0 ou posterior é um dessas bibliotecas que fornece a funcionalidade de simulação recomendada e segue as práticas descritas abaixo.

### <a name="arrangement"></a>Disposição

#### <a name="use-multiple-service-instances"></a>Utilizar várias instâncias do serviço
Testes de unidade devem ser executado várias instâncias de serviço com estado. Isso simula o que realmente acontece no cluster onde o Service Fabric fornece várias réplicas com o seu serviço em nós diferentes. Cada uma destas instâncias estará executando num contexto diferente no entanto. Ao executar o teste, cada instância deve ser preparada com a configuração de função esperada no cluster. Por exemplo, se o serviço deve ter o tamanho da réplica de destino de 3, o Service Fabric seria aprovisionar três réplicas em nós diferentes. Um dos quais sendo principal e as outras duas a ser secundária ativa.

Na maioria dos casos, o caminho de execução de serviço irá variar um pouco para cada uma destas funções. Por exemplo, se o serviço não deve aceitar pedidos de uma secundária ativa, o serviço pode ter uma verificação para este caso volta lançar uma exceção informativa que indica um pedido foi tentada num secundário. Ter várias instâncias, permitirá que essa situação ser testada.

Além disso, ter várias instâncias permite que os testes para alternar as funções de cada uma destas instâncias para verificar que as respostas são consistentes apesar das alterações de função.

#### <a name="mock-the-state-manager"></a>Simular o Gestor de estado
O Gestor de estado deve ser tratado como um recurso remoto e, portanto, simulado. Quando o Gestor de estado de simulação, deve haver algum armazenamento em memória subjacente para controlar o que é guardado para o Gestor de estado para que possam ser lidos e verificado. Uma forma simples de conseguir isso é criar instâncias de simulação de cada um dos tipos de coleções fiáveis. Dentro desses objetos fictícios, utilize um tipo de dados que se alinha com as operações executadas em relação a essa coleção. Seguem-se alguns tipos de dados sugeridos para cada coleção fiável

- IReliableDictionary < TKey, TValue >-System.Collections.Concurrent.ConcurrentDictionary < TKey, TValue > >
- IReliableQueue<T> -> System.Collections.Generic.Queue<T>
- IReliableConcurrentQueue<T> -> System.Collections.Concurrent.ConcurrentQueue<T>

#### <a name="many-state-manager-instances-single-storage"></a>Muitas instâncias de Gestor de estado, de armazenamento
Como mencionado anteriormente, o Gerenciador de estado e as coleções fiáveis devem ser tratadas como um recurso remoto. Por conseguinte, estes recursos devem e serão ser simulados em testes de unidade. No entanto, ao executar várias instâncias de um serviço com monitorização de estado é um desafio para manter cada gestor de estado fictícios em sincronia entre instâncias de serviço com estado diferente. Quando o serviço com estado está em execução no cluster, o Service Fabric encarrega-se de manter o Gerenciador de estado de cada réplica secundária consistente com a réplica primária. Por conseguinte, os testes devem se comportam da mesma, para que eles podem simular as alterações de função.

Uma forma simples, esta sincronização pode ser conseguida, é usar um padrão de singleton para o objeto subjacente que armazena os dados escritos para cada coleção fiável. Por exemplo, se um serviço com monitorização de estado está a utilizar um `IReliableDictionary<string, string>`. O Gestor de estado fictícia deverá devolver uma simulação de `IReliableDictionary<string, string>`. Pode utilizar essa simulação um `ConcurrentDictionary<string, string>` para controlar os pares de chaves/valores escritos. O `ConcurrentDictionary<string, string>` deve ser um singleton utilizado pelas instâncias de todos os gerentes de estado de passada para o serviço.

#### <a name="keep-track-of-cancellation-tokens"></a>Mantenha um registo de cancelamento dos tokens
Cancelamento dos tokens é que uma importante ainda normalmente deixada de lado o aspecto de serviços com estado. Quando o Service Fabric é iniciado uma réplica primária para um serviço com estado, é fornecido um token de cancelamento. Este token de cancelamento destina-se para sinalizar para o serviço quando ele é removido ou rebaixado para uma função diferente. O serviço com estado deve parar quaisquer operações de longa execução ou assíncronas, para que o Service Fabric, pode concluir o fluxo de trabalho de alteração de função.

Quando a unidade a executar os testes, quaisquer tokens de cancelamento que sejam fornecidos à RunAsync, ChangeRoleAsync OpenAsync e CloseAsync devem ser mantida durante a execução de teste. Retenção para estes tokens permitirá que o teste simular um encerramento do serviço ou despromoção e verifique se o serviço responde adequadamente.

#### <a name="test-end-to-end-with-mocked-remote-resources"></a>Teste-a-ponto com recursos remotos simuladas
Testes de unidade devem ser executado como boa parte do código de aplicativo que pode modificar o estado do serviço com estado quanto possível. Recomenda-se que os testes de ser mais ponto-a-ponto por natureza. As simulações únicas que existem estão a registar, simular, e/ou certifique-se de interações de recurso remoto. Isto inclui as interações com o Gestor de estado e as coleções fiáveis. O fragmento seguinte é um exemplo de gherkin para um teste que demonstra o teste de ponto a ponto:

```
    Given stateful service named "fabric:/MyApp/MyService" is created
    And a new replica is created as "Primary" with id "111"
    And a new replica is created as "IdleSecondary" with id "222"
    And a new replica is created as "IdleSecondary" with id "333"
    And all idle secondary replicas are promoted to active secondary
    When a request is made to add the an employee "John Smith"
    And the active secondary replica "222" is promoted to primary
    And a request is made to get all employees
    Then the request should should return the "John Smith" employee
```

Este teste declara que os dados que está a ser capturados na um réplica estão disponíveis para uma réplica secundária, quando ele é promovido para primário. Supondo que uma coleção fiável é o armazenamento de backup para os dados de funcionários, a falha potencial Aa, que poderia ser detectada com este teste é se o código da aplicação não foi executado `CommitAsync` na transação para guardar o novo funcionário. Nesse caso, a segunda solicitação para colocar os funcionários não retornaria funcionário adicionado ao primeiro pedido.

### <a name="acting"></a>Agir
#### <a name="mimic-service-fabric-replica-orchestration"></a>Imitar a orquestração de réplica do Service Fabric
Ao gerenciar várias instâncias do serviço, os testes devem inicializar e subdividir estes serviços da mesma forma como a orquestração do Service Fabric. Por exemplo, quando é criado um serviço numa nova réplica primária, Service Fabric invocará CreateServiceReplicaListener, OpenAsync, ChangeRoleAsync e RunAsync. Os eventos de ciclo de vida estão documentados nos seguintes artigos:

- [Arranque do serviço com estado](service-fabric-reliable-services-lifecycle.md#stateful-service-startup)
- [Encerramento do serviço com estado](service-fabric-reliable-services-lifecycle.md#stateful-service-shutdown)
- [Trocas de principal de serviço com estado](service-fabric-reliable-services-lifecycle.md#stateful-service-primary-swaps)

#### <a name="run-replica-role-changes"></a>Executar alterações de função de réplica
Os testes de unidade devem alterar as funções das instâncias de serviço da mesma forma como a orquestração do Service Fabric. A máquina de estado de função está documentada no seguinte artigo:

[Máquina de estado de função de réplica](service-fabric-concepts-replica-lifecycle.md#replica-role)

Simulação de alterações de função é um dos aspectos mais críticos de teste e pode revelar problemas em que o estado da réplica não são consistentes entre si. Estado da réplica inconsistente pode ocorrer devido ao armazenamento de estado na memória em estático ou variáveis de instância do nível de classe. Exemplos disso podem ser cancelamento dos tokens, enums e objetos de/valores de configuração. Isto irá garantir que o serviço está a respeitar os tokens de cancelamento fornecidos durante o RunAsync, para permitir que a alteração de função ocorrer. Simulação de alterações de função também pode descobrir problemas que podem surgir se o código não é escrito para permitir uma invocação de RunAsync várias vezes.

#### <a name="cancel-cancellation-tokens"></a>Cancelar tokens de cancelamento
Deve existir-se os testes de unidade em que o token de cancelamento fornecido RunAsync é cancelado. Isso permitirá que o teste verificar que o serviço for encerrado. Durante este encerramento quaisquer operações de longa execução ou assíncronas deve ser parado. Exemplo de um processo de longa execução que possam existir num serviço é aquele que está à escuta de mensagens numa fila fiável. Isso pode existir diretamente no RunAsync ou um thread em segundo plano. A implementação deve incluir a lógica para a operação a sair se este token de cancelamento é cancelada.

Se utilizar a serviços com monitoração de estado de qualquer Estado de cache ou dentro da memória só deve existir no principal, deve ser eliminado neste momento. Trata-se para se certificar de que este estado é consistente se o nó se tornar um site primário novamente mais tarde. Teste de cancelamento permitirá que o teste verificar se que este estado é descartado corretamente.

#### <a name="execute-requests-against-multiple-replicas"></a>Executar pedidos contra várias réplicas
---Vyhodnocení testes devem ser executado mesmo pedido em relação a diferente réplica. Quando combinado com alterações de função, problemas de consistência podem ser que não foi abordados. Um teste de exemplo, pode efetuar os seguintes passos:
1. Executar um pedido de escrita contra primário atual
2. Executar uma solicitação de leitura que retorna os dados escritos no passo 1 contra primário atual
3. Promova uma secundária para primária. Isso também deve despromover primário atual para o secundário
4. Execute a mesma solicitação de leitura do passo 2 contra no novo secundário.

No último passo, o teste pode declarar os dados devolvidos são consistentes. Um problema em potencial que isso poderia revelar é que os dados devolvidos pelo serviço podem ser na memória, mas, por fim, apoiado por uma coleção fiável. Esses dados na memória não podem ser mantidas sincronizadas corretamente com o que existe na coleção fiável.

Dados na memória é normalmente utilizados para criar índices secundários ou agregações de dados existentes numa coleção fiável.

### <a name="asserting"></a>Declarando
#### <a name="ensure-responses-match-across-replicas"></a>Certifique-se de que as respostas combine réplicas
Testes de unidade devem afirmar que uma resposta para uma determinada solicitação é consistente em várias réplicas depois que a transição para o primário. Isso pode surgir problemas potenciais em que dados fornecidos na resposta seja não apoiados por uma coleção fiável, ou mantidos na memória sem um mecanismo para sincronizar dados em réplicas. Isto irá garantir que o serviço envia respostas consistentes novamente depois de Service Fabric rebalances ou efetua a ativação pós-falha para uma nova réplica primária.

#### <a name="verify-service-respects-cancellation"></a>Verifique se o serviço respeita a cancelamento
Processos de longa execução ou assíncronos que devem ser encerrados quando um token de cancelamento é cancelado devem ser verificados que eles realmente terminaram após o cancelamento. Isto irá garantir que apesar da réplica alterar funções, processos que não se destinam a manter em execução na réplica não primário interrompida antes da conclusão da transição. Isso também pode revelar problemas em que tal processo bloqueia um pedido de alteração ou encerramento de função do Service Fabric a conclusão.

#### <a name="verify-which-replicas-should-serve-requests"></a>Certifique-se de que as réplicas devem atender a solicitações de
Os testes devem declarar o comportamento esperado, se um pedido é encaminhado para uma réplica não principal. Service Fabric fornece a capacidade de ter as réplicas secundárias servir pedidos. No entanto, apenas podem ocorrem escritas para coleções fiáveis a partir da réplica primária. Se tenciona de seu aplicativo para apenas as réplicas primárias servir pedidos ou apenas um subconjunto de pedidos pode ser processado por uma secundária, os testes devem declarar o comportamento esperado para ambos os casos positivos e negativos. Caso negativo, que está a ser um pedido é encaminhado para uma réplica que não deve processar o pedido e, positivo que está a ser o oposto.

## <a name="next-steps"></a>Passos Seguintes
Saiba como [serviços com estado de teste de unidade](service-fabric-how-to-unit-test-stateful-services.md).