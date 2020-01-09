---
title: Serviços com estado de teste de unidade no Azure Service Fabric
description: Saiba mais sobre os conceitos e as práticas de teste de unidade Service Fabric serviços com estado.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 12e8a47d9685dee12594f4e2afaa848d9688d185
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75433918"
---
# <a name="unit-testing-stateful-services-in-service-fabric"></a>Serviços com estado de teste de unidade no Service Fabric

Este artigo aborda os conceitos e as práticas de teste de unidade Service Fabric serviços com estado. O teste de unidade dentro de Service Fabric merece suas próprias considerações devido ao fato de que o código do aplicativo é executado ativamente em vários contextos diferentes. Este artigo descreve as práticas usadas para garantir que o código do aplicativo seja coberto por cada um dos diferentes contextos que ele pode executar.

## <a name="unit-testing-and-mocking"></a>Teste de unidade e simulação
O teste de unidade no contexto deste artigo é o teste automatizado que pode ser executado dentro do contexto de um executor de teste, como MSTest ou NUnit. Os testes de unidade neste artigo não executam operações em um recurso remoto, como um banco de dados ou uma API RESTFul. Esses recursos remotos devem ser simulados. A simulação no contexto deste artigo irá falsificar, gravar e controlar os valores de retorno para recursos remotos.

### <a name="service-fabric-considerations"></a>Considerações sobre Service Fabric
O teste de unidade de um serviço Service Fabric com estado tem várias considerações. Em primeiro lugar, o código do serviço é executado em vários nós, mas em funções diferentes. Os testes de unidade devem avaliar o código sob cada função para obter cobertura completa. As diferentes funções seriam primárias, secundárias ativas, secundárias ociosas e desconhecidas. Normalmente, a função None não precisa de nenhuma cobertura especial, pois Service Fabric considera que essa função seja nula ou um serviço nulo. Em segundo lugar, cada nó alterará sua função em qualquer ponto determinado. Para obter uma cobertura completa, o caminho de execução de código deve ser testado com a ocorrência de alterações de função.

## <a name="why-unit-test-stateful-services"></a>Por que os serviços com estado de teste de unidade? 
Os serviços com estado de teste de unidade podem ajudar a descobrir alguns erros comuns que não seriam necessariamente detectados pelo aplicativo convencional ou pelo teste de unidade específico de domínio. Por exemplo, se o serviço com estado tiver qualquer estado na memória, esse tipo de teste poderá verificar se esse estado na memória é mantido em sincronia em cada réplica. Esse tipo de teste também pode verificar se um serviço com estado responde a tokens de cancelamento passados pela orquestração de Service Fabric apropriadamente. Quando os cancelamentos são disparados, o serviço deve interromper todas as operações de execução longa e/ou assíncronas.  

## <a name="common-practices"></a>Práticas comuns

A seção a seguir aconselha as práticas mais comuns para o teste de unidade de um serviço com estado. Ele também aconselha o que uma camada fictícia deve ter para alinhar com mais detalhes a orquestração Service Fabric e o gerenciamento de estado. O [infabric. Mocks](https://www.nuget.org/packages/ServiceFabric.Mocks/) a partir de 3.3.0 ou posterior é uma dessas bibliotecas que fornece a funcionalidade de simulação recomendada e segue as práticas descritas abaixo.

### <a name="arrangement"></a>Organização

#### <a name="use-multiple-service-instances"></a>Usar várias instâncias de serviço
Os testes de unidade devem executar várias instâncias de um serviço com estado. Isso simula o que realmente acontece no cluster em que Service Fabric provisiona várias réplicas que executam o serviço em nós diferentes. No entanto, cada uma dessas instâncias será executada em um contexto diferente. Ao executar o teste, cada instância deve ser ativada com a configuração de função esperada no cluster. Por exemplo, se espera-se que o serviço tenha o tamanho de réplica de destino 3, Service Fabric provisionar três réplicas em nós diferentes. Um dos quais o primário e outros dois são secundários ativos.

Na maioria dos casos, o caminho de execução do serviço variará um pouco para cada uma dessas funções. Por exemplo, se o serviço não deve aceitar solicitações de um secundário ativo, o serviço pode ter uma verificação para esse caso para gerar uma exceção informativa que indica que uma solicitação foi tentada em um secundário. Ter várias instâncias permitirá que essa situação seja testada.

Além disso, ter várias instâncias permite que os testes alternem as funções de cada uma dessas instâncias para verificar se as respostas são consistentes, apesar das alterações de função.

#### <a name="mock-the-state-manager"></a>Imitar o Gerenciador de estado
O Gerenciador de Estado deve ser tratado como um recurso remoto e, portanto, simulado. Ao simular o Gerenciador de estado, precisa haver algum armazenamento subjacente na memória para controlar o que é salvo no Gerenciador de estado para que ele possa ser lido e verificado. Uma maneira simples de conseguir isso é criar instâncias fictícias de cada um dos tipos de coleções confiáveis. Nessas simulações, use um tipo de dados que se alinhe fortemente com as operações executadas em relação a essa coleção. A seguir estão alguns tipos de dados sugeridos para cada coleção confiável

- IReliableDictionary < TKey, TValue >-> System. Collections. Current. ConcurrentDictionary < TKey, TValue >
- IReliableQueue\<T >-> System. Collections. Generic. Queue\<T >
- IReliableConcurrentQueue\<T >-> System. Collections. Current. ConcurrentQueue\<T >

#### <a name="many-state-manager-instances-single-storage"></a>Muitas instâncias do Gerenciador de estado, armazenamento único
Como mencionado anteriormente, o Gerenciador de estado e as coleções confiáveis devem ser tratadas como um recurso remoto. Portanto, esses recursos devem e serão simulados dentro dos testes de unidade. No entanto, ao executar várias instâncias de um serviço com estado, será um desafio manter cada Gerenciador de estado fictício sincronizado entre diferentes instâncias de serviço com estado. Quando o serviço com estado está em execução no cluster, o Service Fabric cuida de manter cada Gerenciador de estado da réplica secundária consistente com a réplica primária. Portanto, os testes devem se comportar da mesma forma que podem simular alterações de função.

Uma maneira simples de atingir essa sincronização é usar um padrão singleton para o objeto subjacente que armazena os dados gravados em cada coleção confiável. Por exemplo, se um serviço com estado estiver usando um `IReliableDictionary<string, string>`. O Gerenciador de estado de simulação deve retornar uma simulação de `IReliableDictionary<string, string>`. Essa simulação pode usar um `ConcurrentDictionary<string, string>` para controlar os pares de chave/valor gravados. O `ConcurrentDictionary<string, string>` deve ser um singleton usado por todas as instâncias dos gerenciadores de estado passadas para o serviço.

#### <a name="keep-track-of-cancellation-tokens"></a>Controlar os tokens de cancelamento
Tokens de cancelamento são um aspecto importante, mas geralmente ignorado pelos serviços com estado. Quando Service Fabric inicia uma réplica primária para um serviço com estado, um token de cancelamento é fornecido. Esse token de cancelamento destina-se a sinalizar para o serviço quando ele é removido ou rebaixado para uma função diferente. O serviço com estado deve parar todas as operações assíncronas ou de execução longa para que Service Fabric possa concluir o fluxo de trabalho de alteração de função.

Ao executar os testes de unidade, todos os tokens de cancelamento que são fornecidos para RunAsync, ChangeRoleAsync, OpenAsync e CloseAsync devem ser mantidos durante a execução do teste. Manter esses tokens permitirá que o teste simule um desligamento ou rebaixamento do serviço e verifique se o serviço responde adequadamente.

#### <a name="test-end-to-end-with-mocked-remote-resources"></a>Teste de ponta a ponta com recursos remotos simulados
Os testes de unidade devem ser executados na maior parte do código do aplicativo, o que pode modificar o estado do serviço com estado possível. É recomendável que os testes sejam mais de ponta a ponta por natureza. As únicas simulações que existem são registrar, simular e/ou verificar interações de recursos remotos. Isso inclui interações com o Gerenciador de estado e as coleções confiáveis. O trecho a seguir é um exemplo de Gherkin para um teste que demonstra testes de ponta a ponta:

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

Esse teste declara que os dados que estão sendo capturados em uma réplica estão disponíveis para uma réplica secundária quando ela é promovida para o primário. Supondo que uma coleção confiável seja o armazenamento de backup para os dados do funcionário, a falha em potencial AA que poderia ser detectada com esse teste é se o código do aplicativo não tiver sido executado `CommitAsync` na transação para salvar o novo funcionário. Nesse caso, a segunda solicitação para obter funcionários não retornaria funcionário adicionado pela primeira solicitação.

### <a name="acting"></a>Funcione
#### <a name="mimic-service-fabric-replica-orchestration"></a>Coordenação Service Fabric orquestração de réplica
Ao gerenciar várias instâncias de serviço, os testes devem inicializar e desmontar esses serviços da mesma maneira que a orquestração de Service Fabric. Por exemplo, quando um serviço é criado em uma nova réplica primária, Service Fabric invocará CreateServiceReplicaListener, OpenAsync, ChangeRoleAsync e RunAsync. Os eventos do ciclo de vida são documentados nos seguintes artigos:

- [Inicialização do serviço com estado](service-fabric-reliable-services-lifecycle.md#stateful-service-startup)
- [Desligamento de serviço com estado](service-fabric-reliable-services-lifecycle.md#stateful-service-shutdown)
- [Trocas primárias de serviço com estado](service-fabric-reliable-services-lifecycle.md#stateful-service-primary-swaps)

#### <a name="run-replica-role-changes"></a>Executar alterações de função de réplica
Os testes de unidade devem alterar as funções das instâncias de serviço da mesma maneira que a orquestração de Service Fabric. O computador de estado de função é documentado no seguinte artigo:

[Máquina de estado da função de réplica](service-fabric-concepts-replica-lifecycle.md#replica-role)

A simulação de alterações de função é um dos aspectos mais críticos do teste e pode revelar problemas em que o estado da réplica não é consistente entre si. O estado de réplica inconsistente pode ocorrer devido ao armazenamento de estado na memória em variáveis de instância estática ou de nível de classe. Exemplos disso podem ser tokens de cancelamento, enums e objetos/valores de configuração. Isso também garantirá que o serviço esteja respeitando os tokens de cancelamento fornecidos durante a RunAsync para permitir que a alteração da função ocorra. A simulação de alterações de função também pode revelar problemas que podem surgir se o código não for escrito para permitir uma invocação de RunAsync várias vezes.

#### <a name="cancel-cancellation-tokens"></a>Cancelar tokens de cancelamento
Deve existir testes de unidade em que o token de cancelamento fornecido para RunAsync seja cancelado. Isso permitirá que o teste Verifique se o serviço é desligado normalmente. Durante esse desligamento, todas as operações assíncronas ou de longa execução devem ser interrompidas. Um exemplo de processo de execução demorada que pode existir em um serviço é aquele que escuta mensagens em uma fila confiável. Isso pode existir diretamente no RunAsync ou em um thread em segundo plano. A implementação deve incluir a lógica para sair da operação se esse token de cancelamento for cancelado.

Se os serviços com estado fizerem uso de qualquer estado de cache ou na memória que só deva existir no primário, ele deverá ser descartado no momento. Isso é para garantir que esse Estado seja consistente se o nó se tornar primário novamente mais tarde. O teste de cancelamento permitirá que o teste Verifique se esse Estado foi descartado corretamente.

#### <a name="execute-requests-against-multiple-replicas"></a>Executar solicitações em várias réplicas
Os testes de declaração devem executar a mesma solicitação em relação a diferentes réplicas. Quando emparelhadas com alterações de função, os problemas de consistência podem ser descobertos. Um teste de exemplo pode executar as seguintes etapas:
1. Executar uma solicitação de gravação no primário atual
2. Executar uma solicitação de leitura que retorna os dados gravados na etapa 1 em relação ao primário atual
3. Promover um secundário para o primário. Isso também deve rebaixar o primário atual para o secundário
4. Execute a mesma solicitação de leitura da etapa 2 em relação ao novo secundário.

Na última etapa, o teste pode afirmar que os dados retornados são consistentes. Um problema potencial que isso poderia descobrir é que os dados que estão sendo retornados pelo serviço podem estar na memória, mas apoiados por fim por uma coleção confiável. Esses dados na memória podem não ser mantidos em sincronia corretamente com o que existe na coleção confiável.

Os dados na memória normalmente são usados para criar índices secundários ou agregações de dados existentes em uma coleção confiável.

### <a name="asserting"></a>Declarando
#### <a name="ensure-responses-match-across-replicas"></a>Garantir correspondência de respostas entre réplicas
Os testes de unidade devem declarar que uma resposta para uma determinada solicitação é consistente em várias réplicas após a transição para o primário. Isso pode trazer possíveis problemas nos quais os dados fornecidos na resposta não são apoiados por uma coleção confiável ou mantidos na memória sem um mecanismo para sincronizar esses dados entre réplicas. Isso garantirá que o serviço envie respostas de back-up após Service Fabric reequilibra ou faça o failover para uma nova réplica primária.

#### <a name="verify-service-respects-cancellation"></a>Verificar o cancelamento dos aspectos do serviço
Processos assíncronos ou de execução longa que devem ser encerrados quando um token de cancelamento é cancelado devem ser verificados de fato que eles foram encerrados após o cancelamento. Isso garantirá que, apesar da réplica que altera as funções, os processos que não se destinam a manter a execução na réplica não primária pare antes da conclusão da transição. Isso também pode revelar problemas em que um processo desse tipo bloqueia a conclusão de uma solicitação de alteração de função ou de desligamento de Service Fabric.

#### <a name="verify-which-replicas-should-serve-requests"></a>Verificar quais réplicas devem atender a solicitações
Os testes devem declarar o comportamento esperado se uma solicitação for roteada para uma réplica não primária. Service Fabric fornece a capacidade de fazer com que as réplicas secundárias atendam às solicitações. No entanto, as gravações em coleções confiáveis só podem ocorrer a partir da réplica primária. Se o seu aplicativo pretende apenas réplicas primárias para atender a solicitações ou apenas um subconjunto de solicitações pode ser manipulado por um secundário, os testes devem declarar o comportamento esperado para os casos positivos e negativos. O caso negativo sendo uma solicitação é roteado para uma réplica que não deve lidar com a solicitação e, o que é positivo como o oposto.

## <a name="next-steps"></a>Passos seguintes
Saiba como [testar os serviços com estado de teste de unidade](service-fabric-how-to-unit-test-stateful-services.md).
