---
title: Unidade de teste de serviços estatais em Tecido de Serviço Azure
description: Conheça os conceitos e práticas de teste unitário Serviço de Serviço De Serviço Sinuoso Serviços.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 12e8a47d9685dee12594f4e2afaa848d9688d185
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75433918"
---
# <a name="unit-testing-stateful-services-in-service-fabric"></a>Unidade de teste de serviços estatais em Tecido de Serviço

Este artigo abrange os conceitos e práticas de teste unitário Serviço de Serviço De Serviço De Serviço Sinuoso Serviços. O teste de unidade dentro do Tecido de Serviço merece as suas próprias considerações devido ao facto de o código de aplicação ser executado ativamente sob vários contextos diferentes. Este artigo descreve as práticas utilizadas para garantir que o código de aplicação seja coberto em cada um dos diferentes contextos que pode executar.

## <a name="unit-testing-and-mocking"></a>Teste de unidade e zombagem
O teste de unidade no contexto deste artigo é um teste automatizado que pode ser executado no contexto de um test runner como MSTest ou NUnit. Os testes da unidade neste artigo não realizam operações contra um recurso remoto, como uma base de dados ou a API RESTFul. Estes recursos remotos devem ser ridicularizados. A zombaria no contexto deste artigo irá falsificar, gravar e controlar os valores de retorno para recursos remotos.

### <a name="service-fabric-considerations"></a>Considerações de Tecido de Serviço
A unidade que testa um serviço de estado de tecido de serviço tem várias considerações. Em primeiro lugar, o código de serviço executa em vários nós, mas sob diferentes funções. Os testes unitários devem avaliar o código em cada função para obter uma cobertura completa. Os diferentes papéis seriam Primário, Secundário Ativo, Secundário Inativo e Desconhecido. A função None normalmente não precisa de qualquer cobertura especial, uma vez que a Service Fabric considera que esta função é um serviço nulo ou nulo. Em segundo lugar, cada nó mudará o seu papel em qualquer ponto. Para obter uma cobertura completa, os caminhos de execução de código sem devem ser testados com alterações de papéis que ocorram.

## <a name="why-unit-test-stateful-services"></a>Por que a unidade testa serviços estatais? 
Os serviços de teste de unidade podem ajudar a descobrir alguns erros comuns que são cometidos que não seriam necessariamente apanhados por aplicação convencional ou testes de unidade específicos de domínio. Por exemplo, se o serviço imponente tiver algum estado de memória, este tipo de teste pode verificar se este estado de memória é mantido sincronizado em cada réplica. Este tipo de testes também pode verificar que um serviço imponente responde a fichas de cancelamento transmitidas pela orquestração de Tecido de Serviço adequadamente. Quando os cancelamentos são desencadeados, o serviço deve parar qualquer operação de longa duração e/ou assíncrona.  

## <a name="common-practices"></a>Práticas comuns

A secção seguinte aconselha sobre as práticas mais comuns para a unidade que testa um serviço imponente. Também aconselha o que uma camada de zombaria deve alinhar de perto com a orquestração do Tecido de Serviço e a gestão do Estado. [ServiceFabric.Mocks](https://www.nuget.org/packages/ServiceFabric.Mocks/) a partir de 3.3.0 ou mais tarde é uma dessas bibliotecas que fornece a funcionalidade de zomridão recomendada e segue as práticas descritas abaixo.

### <a name="arrangement"></a>Arranjo

#### <a name="use-multiple-service-instances"></a>Use várias instâncias de serviço
Os testes unitários devem executar várias instâncias de um serviço de estado. Isto simula o que realmente acontece no cluster onde o Service Fabric disponibiliza múltiplas réplicas que executam o seu serviço em diferentes nós. No entanto, cada um destes casos será executado num contexto diferente. Ao executar o teste, cada instância deve ser preparada com a configuração de função esperada no cluster. Por exemplo, se se espera que o serviço tenha o tamanho da réplica alvo de 3, o Service Fabric forneceria três réplicas em nós diferentes. Um dos quais é o principal e os outros dois da Ative Secondary.

Na maioria dos casos, o caminho de execução de serviço variará ligeiramente para cada uma destas funções. Por exemplo, se o serviço não aceitar pedidos de um Secundário Ativo, o serviço pode ter um cheque para este caso para repor uma exceção informativa que indique que um pedido foi tentado num secundário. Ter vários casos permitirá que esta situação seja testada.

Além disso, ter várias instâncias permite que os testes alterem as funções de cada uma destas instâncias para verificar se as respostas são consistentes apesar das mudanças de papel.

#### <a name="mock-the-state-manager"></a>Goze com o gerente do Estado.
O Gerente do Estado deve ser tratado como um recurso remoto e, portanto, gozado. Ao ridicularizar o gerente do Estado, tem de haver algum armazenamento subjacente na memória para rastrear o que é guardado ao gerente do Estado para que possa ser lido e verificado. Uma forma simples de o conseguir é criar casos falsos de cada um dos tipos de Coleções Fiáveis. Dentro dessas simulações, utilize um tipo de dados que se alinha de perto com as operações realizadas contra essa recolha. Seguem-se alguns tipos de dados sugeridos para cada recolha fiável

- IReliableDictionary<TKey, TValue> -> System.Collections.Concurrent.ConcurrentDictionary<TKey, TValue>
- IReliableQueue\<T> -> System.Collections.Generic.Queue\<T>
- IReliableConcurrentQueue\<T> -> System.Collections.ConcurrentQueue\<T>

#### <a name="many-state-manager-instances-single-storage"></a>Muitos casos de gerente de estado, armazenamento único
Como mencionado anteriormente, o Gerente do Estado e as Coleções Fiáveis devem ser tratados como um recurso remoto. Por conseguinte, estes recursos devem e serão ridicularizados nos testes unitários. No entanto, ao executar várias instâncias de um serviço imponente, será um desafio manter cada gerente do Estado ridicularizado em sincronização em diferentes instâncias de serviço sinuosos. Quando o serviço imponente está em execução no cluster, o Tecido de Serviço cuida de manter o gestor estatal de cada réplica secundária consistente com a réplica primária. Por isso, os testes devem comportar-se da mesma forma para que possam simular alterações de papéis.

Uma forma simples de alcançar esta sincronização, é usar um padrão singleton para o objeto subjacente que armazena os dados escritos a cada Coleção Fiável. Por exemplo, se um serviço `IReliableDictionary<string, string>`imponente estiver a utilizar um . O gerente do estado deve `IReliableDictionary<string, string>`retribuir uma simulação de. Essa simulação `ConcurrentDictionary<string, string>` pode usar um para acompanhar os pares chave/valor escritos. Deve `ConcurrentDictionary<string, string>` ser um singleton usado por todos os casos dos gestores estatais passados para o serviço.

#### <a name="keep-track-of-cancellation-tokens"></a>Acompanhe as fichas de cancelamento
As fichas de cancelamento são um aspeto importante mas comumente negligenciado dos serviços estatais. Quando o Service Fabric inicia uma réplica primária para um serviço imponente, é fornecido um símbolo de cancelamento. Este símbolo de cancelamento destina-se a sinalizar ao serviço quando este é removido ou despromovido para uma função diferente. O serviço imponente deve parar qualquer operação de longa duração ou assíncrona para que o Tecido de Serviço possa completar o fluxo de trabalho de mudança de funções.

Ao executar os testes da unidade, quaisquer fichas de cancelamento fornecidas a RunAsync, ChangeRoleAsync, OpenAsync e CloseAsync devem ser realizadas durante a execução do teste. Segurar estas fichas permitirá que o teste simule uma paragem ou despromoção de serviço e verifique se o serviço responde adequadamente.

#### <a name="test-end-to-end-with-mocked-remote-resources"></a>Teste de ponta a ponta com recursos remotos simulados
Os testes unitários devem executar o máximo do código de aplicação que possa modificar o estado do serviço de estado possível. Recomenda-se que os testes sejam mais de ponta a ponta na natureza. As únicas simulações que existem são gravar, simular e/ou verificar interações remotas de recursos. Isto inclui interações com o Gerente do Estado e Coleções Fiáveis. O seguinte corte é um exemplo de pepino para um teste que demonstra testes de ponta a ponta:

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

Este teste afirma que os dados capturados numa réplica estão disponíveis para uma réplica secundária quando é promovido ao primário. Assumindo que uma recolha fiável é a reserva para os dados dos empregados, a falha `CommitAsync` potencial da Aa que poderia ser apanhada com este teste é se o código de aplicação não executasse na transação para salvar o novo funcionário. Nesse caso, o segundo pedido para obter os empregados não devolveria o trabalhador adicionado pelo primeiro pedido.

### <a name="acting"></a>Atuação
#### <a name="mimic-service-fabric-replica-orchestration"></a>Orquestração de réplica de tecido de serviço mimic
Ao gerir várias instâncias de serviço, os testes devem inicializar e demolir estes serviços da mesma forma que a orquestração do Tecido de Serviço. Por exemplo, quando um serviço é criado numa nova réplica primária, o Tecido de Serviço invocará CreateServiceReplicaListener, OpenAsync, ChangeRoleAsync e RunAsync. Os eventos de ciclo de vida são documentados nos seguintes artigos:

- [Startup de serviço sinuoso](service-fabric-reliable-services-lifecycle.md#stateful-service-startup)
- [Encerramento de serviços estatais](service-fabric-reliable-services-lifecycle.md#stateful-service-shutdown)
- [Trocas primárias de serviço sinuoso](service-fabric-reliable-services-lifecycle.md#stateful-service-primary-swaps)

#### <a name="run-replica-role-changes"></a>Executar mudanças de papel de réplica
Os testes unitários devem alterar as funções das instâncias de serviço da mesma forma que a orquestração do Tecido de Serviço. O papel da máquina do Estado está documentado no seguinte artigo:

[Máquina de estado de réplica](service-fabric-concepts-replica-lifecycle.md#replica-role)

Simular mudanças de papel é um dos aspetos mais críticos dos testes e pode descobrir questões em que o estado da réplica não é consistente uns com os outros. O estado de réplica inconsistente pode ocorrer devido ao armazenamento do estado de memória em variáveis de instância estática ou de nível de classe. Exemplos disso podem ser tokens de cancelamento, enums e objetos/valores de configuração. Isto também garantirá que o serviço está respeitando as fichas de cancelamento fornecidas durante o RunAsync para permitir a mudança de função. Simular alterações de papéis também pode descobrir questões que podem surgir se o código não for escrito para permitir uma invocação de RunAsync várias vezes.

#### <a name="cancel-cancellation-tokens"></a>Cancelar fichas de cancelamento
Devem existir testes unitários onde o token de cancelamento fornecido à RunAsync é cancelado. Isto permitirá que o teste verifique se o serviço desliga graciosamente. Durante este encerramento, devem ser interrompidas quaisquer operações de longa duração ou assíncronas. Exemplo de um processo de longa duração que pode existir num serviço é aquele que ouve mensagens numa Fila Fiável. Isto pode existir diretamente dentro de RunAsync ou de um fio de fundo. A implementação deve incluir lógica para sair da operação se este símbolo de cancelamento for cancelado.

Se os serviços estatais utilizarem qualquer cache ou estado de memória que só deva existir no primário, deve ser eliminado neste momento. Isto é para garantir que este estado seja consistente se o nó se tornar uma primária novamente mais tarde. Os testes de cancelamento permitirão que o teste verifique se este estado está devidamente eliminado.

#### <a name="execute-requests-against-multiple-replicas"></a>Executar pedidos contra várias réplicas
Os testes de afirmação devem executar o mesmo pedido contra diferentes réplicas. Quando emparelhadocom mudanças de papéis, as questões de consistência podem ser descobertas. Um teste de exemplo pode executar os seguintes passos:
1. Execute um pedido de escrita contra as primárias atuais
2. Execute um pedido de leitura que rederee os dados escritos no passo 1 contra as primárias atuais
3. Promover um secundário para primário. Isto também deve despromover o primário atual para secundário
4. Execute o mesmo pedido de leitura do passo 2 contra o novo secundário.

No último passo, o teste pode afirmar que os dados devolvidos são consistentes. Um problema potencial que isto poderia descobrir é que os dados que estão a ser devolvidos pelo serviço podem estar na memória, mas apoiados em última análise por uma recolha fiável. Que os dados na memória podem não ser mantidos em sincronização adequada com o que existe na recolha fiável.

Os dados na memória são normalmente usados para criar índices secundários ou agregações de dados que existem numa recolha fiável.

### <a name="asserting"></a>Afirmando
#### <a name="ensure-responses-match-across-replicas"></a>Garantir respostas correspondem entre réplicas
Os testes unitários devem afirmar que uma resposta para um determinado pedido é consistente em várias réplicas após a transição para o primário. Isto pode surgir em questões potenciais em que os dados fornecidos na resposta não são apoiados por uma recolha fiável, ou mantidos na memória sem um mecanismo para sincronizar esses dados através de réplicas. Isto garantirá que o serviço reenvie respostas consistentes após o Reequilíbrio do Tecido de Serviço ou falha numa nova réplica primária.

#### <a name="verify-service-respects-cancellation"></a>Verificar o cancelamento de respeitos do serviço
Processos de longa duração ou assíncronos que devem ser encerrados quando um token de cancelamento é cancelado devem ser verificados que realmente terminaram após o cancelamento. Isto garantirá que, apesar da réplica mudar de funções, processos que não se destinam a continuar a funcionar em paragem de réplicas não primárias antes da transição terminar. Isto também pode descobrir questões em que tal processo bloqueia uma mudança de papéis ou um pedido de encerramento da Service Fabric de completar.

#### <a name="verify-which-replicas-should-serve-requests"></a>Verifique quais as réplicas que devem servir pedidos
Os testes devem afirmar o comportamento esperado se um pedido for encaminhado para uma réplica não primária. O Service Fabric fornece a capacidade de ter réplicas secundárias que servem pedidos. No entanto, escreve para coleções fiáveis que só podem ocorrer a partir da réplica primária. Se a sua aplicação pretende apenas réplicas primárias para servir pedidos ou, apenas um subconjunto de pedidos pode ser tratado por um secundário, então os testes devem afirmar o comportamento esperado tanto para os casos positivos como negativos. O caso negativo é um pedido encaminhado para uma réplica que não deve lidar com o pedido e, sendo o lado positivo o contrário.

## <a name="next-steps"></a>Passos seguintes
Aprenda a [equipar serviços de estado](service-fabric-how-to-unit-test-stateful-services.md)de teste.
