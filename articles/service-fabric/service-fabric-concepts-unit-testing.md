---
title: Unidade de teste de serviços estatais em Azure Service Fabric
description: Conheça os conceitos e práticas de serviço de teste de unidade Serviços Stateful Services.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 12e8a47d9685dee12594f4e2afaa848d9688d185
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "75433918"
---
# <a name="unit-testing-stateful-services-in-service-fabric"></a>Unidade de teste de serviços estatais em Tecido de Serviço

Este artigo abrange os conceitos e práticas de teste unitário serviço de serviços stateful Services. O teste de unidade dentro do Service Fabric merece as suas próprias considerações devido ao facto de o código de aplicação ser executado ativamente em vários contextos diferentes. Este artigo descreve as práticas utilizadas para garantir que o código de aplicação é coberto por cada um dos diferentes contextos que pode executar.

## <a name="unit-testing-and-mocking"></a>Teste de unidade e zombaria
Os testes unitários no contexto deste artigo são testes automatizados que podem ser executados no contexto de um corredor de testes como MSTest ou NUnit. Os testes de unidade dentro deste artigo não efetuam operações contra um recurso remoto, como uma base de dados ou API RESTFUL. Estes recursos remotos devem ser ridicularizados. A zombaria no contexto deste artigo falsificará, registará e controlará os valores de retorno dos recursos remotos.

### <a name="service-fabric-considerations"></a>Considerações de Tecido de Serviço
A unidade de teste de um serviço stateful de Service Fabric tem várias considerações. Em primeiro lugar, o código de serviço executa em múltiplos nós, mas sob funções diferentes. Os testes unitários devem avaliar o código sob cada função para obter uma cobertura completa. Os diferentes papéis seriam Primário, Secundário Ativo, Secundário Idle e Desconhecido. A função Zero normalmente não precisa de qualquer cobertura especial, uma vez que a Service Fabric considera que esta função é um serviço nulo ou nulo. Em segundo lugar, cada nó mudará o seu papel em qualquer ponto. Para obter uma cobertura completa, os caminhos de execução de código devem ser testados com alterações de função que ocorrem.

## <a name="why-unit-test-stateful-services"></a>Por que a unidade testa serviços estatais? 
Os serviços estatais de testes unitários podem ajudar a descobrir alguns erros comuns que são cometidos que não seriam necessariamente apanhados por aplicações convencionais ou testes de unidade específicos de domínio. Por exemplo, se o serviço imponente tiver algum estado de memória, este tipo de teste pode verificar se este estado de memória é mantido sincronizado em cada réplica. Este tipo de teste também pode verificar se um serviço stateful responde a fichas de cancelamento passadas pela orquestração de Tecido de Serviço adequadamente. Quando os cancelamentos são desencadeados, o serviço deve parar qualquer longa execução e/ou operações assíncronos.  

## <a name="common-practices"></a>Práticas comuns

A secção seguinte aconselha sobre as práticas mais comuns para o teste de unidade de um serviço estatal. Também aconselha o que uma camada de zombaria deve ter de se alinhar de perto com a orquestração do Tecido de Serviço e a gestão do estado. [ServiceFabric.Mocks](https://www.nuget.org/packages/ServiceFabric.Mocks/) a partir de 3.3.0 ou mais tarde é uma dessas bibliotecas que fornece a funcionalidade de zombaria recomendada e segue as práticas descritas abaixo.

### <a name="arrangement"></a>Acordo

#### <a name="use-multiple-service-instances"></a>Use várias instâncias de serviço
Os testes de unidade devem executar várias instâncias de um serviço estatal. Isto simula o que realmente acontece no cluster onde o Service Fabric fornece múltiplas réplicas executando o seu serviço através de diferentes nós. No entanto, cada um destes casos será executado num contexto diferente. Ao executar o teste, cada instância deve ser preparada com a configuração de função esperada no cluster. Por exemplo, se se espera que o serviço tenha o tamanho de réplica de 3, o Service Fabric providenciaria três réplicas em diferentes nós. Um deles é o principal e os outros dois são do Ative Secondary.

Na maioria dos casos, o caminho de execução do serviço variará ligeiramente para cada uma destas funções. Por exemplo, se o serviço não aceitar pedidos de um Secundário Ativo, o serviço pode ter um cheque para este caso para desativar uma exceção informativa que indica que um pedido foi tentado num secundário. Ter múltiplas instâncias permitirá que esta situação seja testada.

Além disso, ter múltiplas instâncias permite que os testes alterem as funções de cada uma destas instâncias para verificar se as respostas são consistentes apesar das mudanças de função.

#### <a name="mock-the-state-manager"></a>Goze com o gerente do Estado
O Gerente do Estado deve ser tratado como um recurso remoto e, portanto, ridicularizado. Ao gozar com o gerente do Estado, tem de haver algum armazenamento na memória subjacente para rastrear o que é guardado para o gestor do Estado para que possa ser lido e verificado. Uma forma simples de o conseguir é criar casos falsos de cada um dos tipos de Coleções Fiáveis. Dentro dessas simulações, utilize um tipo de dados que se alinhe estreitamente com as operações realizadas contra essa recolha. Seguem-se alguns tipos de dados sugeridos para cada recolha fiável

- IReliableDictionary<TKey, TValue> -> System.Collections.Concurrent.ConcurrentDictionary<TKey, TValue>
- IReliableQueue \<T> -> System.Collections.Generic.Queue\<T>
- IReliableConcurrentQueue \<T> -> System.Collections.Concurrent.ConcurrentQueue\<T>

#### <a name="many-state-manager-instances-single-storage"></a>Muitas instâncias do Gerente de Estado, armazenamento único
Como mencionado anteriormente, o Gerente de Estado e as Cobranças Fiáveis devem ser tratados como um recurso remoto. Por conseguinte, estes recursos devem e serão ridicularizados nos testes de unidade. No entanto, ao executar várias instâncias de um serviço estatal, será um desafio manter cada gerente do Estado ridicularizado em sincronização em diferentes instâncias de serviço. Quando o serviço imponente está a funcionar no cluster, o Service Fabric cuida de manter o gestor de estado de cada réplica secundária consistente com a réplica primária. Por isso, os testes devem comportar-se da mesma forma para que possam simular alterações de função.

Uma forma simples de conseguir esta sincronização é usar um padrão singleton para o objeto subjacente que armazena os dados escritos a cada Coleção Fiável. Por exemplo, se um serviço estatal estiver a utilizar um `IReliableDictionary<string, string>` . O gerente do Estado deve devolver uma troça de `IReliableDictionary<string, string>` . Essa simulação pode usar um `ConcurrentDictionary<string, string>` para acompanhar os pares de chave/valor escritos. Deve `ConcurrentDictionary<string, string>` ser um singleton usado por todos os casos dos gestores do Estado passados para o serviço.

#### <a name="keep-track-of-cancellation-tokens"></a>Acompanhe os tokens de cancelamento
Os tokens de cancelamento são um aspeto importante mas geralmente negligenciado dos serviços estatais. Quando o Service Fabric inicia uma réplica primária para um serviço imponente, é fornecido um sinal de cancelamento. Este sinal de cancelamento destina-se a sinalizar para o serviço quando este é removido ou despromovido para um papel diferente. O serviço estatal deve parar qualquer longa execução ou operações assíncronos para que o Service Fabric possa completar o fluxo de trabalho de mudança de função.

Ao executar os testes de unidade, quaisquer fichas de cancelamento fornecidas ao RunAsync, ChangeRoleAsync, OpenAsync e CloseAsync devem ser realizadas durante a execução do teste. A manutenção destes tokens permitirá ao teste simular uma paragem ou despromoção de serviço e verificar se o serviço responde adequadamente.

#### <a name="test-end-to-end-with-mocked-remote-resources"></a>Teste de ponta a ponta com recursos remotos zombados
Os testes de unidade devem executar o máximo do código de aplicação que pode modificar o estado do serviço estatal o mais possível. Recomenda-se que os testes sejam mais de ponta a ponta na natureza. As únicas zombarias que existem são para gravar, simular e/ou verificar interações remotas de recursos. Isto inclui interações com o Gerente de Estado e Coleções Fiáveis. O seguinte corte é um exemplo de pepino para um teste que demonstra testes de ponta a ponta:

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

Este teste afirma que os dados capturados numa réplica estão disponíveis para uma réplica secundária quando são promovidos a primário. Assumindo que uma recolha fiável é a loja de apoio para os dados dos colaboradores, a Aa potencial falha que poderia ser apanhada com este teste é se o código de aplicação não executou `CommitAsync` na transação para salvar o novo funcionário. Nesse caso, o segundo pedido de aquisição de funcionários não devolveria o trabalhador adicionado ao primeiro pedido.

### <a name="acting"></a>Agir
#### <a name="mimic-service-fabric-replica-orchestration"></a>Orquestração de réplica de tecido de serviço mimic
Ao gerir várias instâncias de serviço, os testes devem inicializar e demolir estes serviços da mesma forma que a orquestração de Tecido de Serviço. Por exemplo, quando um serviço é criado numa nova réplica primária, o Service Fabric invocará CreateServiceReplicaListener, OpenAsync, ChangeRoleAsync e RunAsync. Os eventos do ciclo de vida estão documentados nos seguintes artigos:

- [Stateful Service Startup](service-fabric-reliable-services-lifecycle.md#stateful-service-startup)
- [Encerramento de serviços imponentes](service-fabric-reliable-services-lifecycle.md#stateful-service-shutdown)
- [Trocas primárias de serviços estatais](service-fabric-reliable-services-lifecycle.md#stateful-service-primary-swaps)

#### <a name="run-replica-role-changes"></a>Executar alterações de função de réplica
Os testes de unidade devem alterar os papéis das instâncias de serviço da mesma forma que a orquestração de Tecido de Serviço. A máquina estatal de função está documentada no seguinte artigo:

[Máquina de estado de função réplica](service-fabric-concepts-replica-lifecycle.md#replica-role)

Simular alterações de funções é um dos aspetos mais críticos dos testes e pode descobrir problemas em que o estado da réplica não é consistente entre si. O estado de réplica inconsistente pode ocorrer devido ao armazenamento do estado de memória em variáveis de instância estática ou de nível de classe. Exemplos disso podem ser tokens de cancelamento, enums e objetos/valores de configuração. Isto também garantirá que o serviço está respeitando as fichas de cancelamento fornecidas durante o RunAsync para permitir que a mudança de função ocorra. Simular alterações de funções também pode descobrir problemas que podem surgir se o código não for escrito para permitir uma invocação de RunAsync várias vezes.

#### <a name="cancel-cancellation-tokens"></a>Cancelar fichas de cancelamento
Deve existir testes de unidade em que o token de cancelamento fornecido ao RunAsync seja cancelado. Isto permitirá que o teste verifique se o serviço desliga graciosamente. Durante este encerramento, quaisquer operações de longa duração ou assíncronos devem ser interrompidas. Exemplo de um processo de longa duração que pode existir num serviço é aquele que ouve mensagens numa fila fiável. Isto pode existir diretamente dentro do RunAsync ou de um fio de fundo. A implementação deve incluir lógica para sair da operação se este token de cancelamento for cancelado.

Se os serviços estatais utilizarem qualquer cache ou estado de memória que só deve existir nas primárias, deve ser eliminado neste momento. Isto é para garantir que este estado seja consistente se o nó se tornar um primário novamente mais tarde. Os testes de cancelamento permitirão que o teste verifique se este estado está devidamente eliminado.

#### <a name="execute-requests-against-multiple-replicas"></a>Executar pedidos contra várias réplicas
Os testes de afirmação devem executar o mesmo pedido contra réplicas diferentes. Quando emparelhados com mudanças de função, as questões de consistência podem ser descobertas. Um teste de exemplo pode efetuar os seguintes passos:
1. Execute um pedido de escrita contra as primárias atuais
2. Execute um pedido de leitura que devolve os dados escritos no passo 1 contra as primárias atuais
3. Promover um secundário para o primário. Isto também deve despromutar a atual primária para secundária
4. Execute o mesmo pedido de leitura do passo 2 contra o novo secundário.

No último passo, o teste pode afirmar que os dados devolvidos são consistentes. Um problema potencial que isto poderia descobrir é que os dados que estão a ser devolvidos pelo serviço podem estar na memória, mas apoiados em última análise por uma recolha fiável. Que os dados na memória podem não ser mantidos em sincronização adequadamente com o que existe na recolha fiável.

Os dados na memória são normalmente utilizados para criar índices secundários ou agregações de dados que existem numa recolha fiável.

### <a name="asserting"></a>Afirmação
#### <a name="ensure-responses-match-across-replicas"></a>Garantir que as respostas correspondem a réplicas
Os testes unitários devem afirmar que uma resposta para um determinado pedido é consistente em várias réplicas após a transição para o primário. Isto pode surgir em potenciais problemas em que os dados fornecidos na resposta não são apoiados por uma recolha fiável, ou mantidos na memória sem um mecanismo para sincronizar esses dados através de réplicas. Isto garantirá que o serviço reencamite respostas consistentes após o reequilíbrio do Tecido de Serviço ou falha numa nova réplica primária.

#### <a name="verify-service-respects-cancellation"></a>Verificar o cancelamento do serviço
Processos de longa duração ou assíncronos que devem ser encerrados quando um token de cancelamento é cancelado devem ser verificados que eles realmente terminaram após o cancelamento. Isto garantirá que, apesar da réplica de alterar funções, processos que não se destinam a continuar a funcionar em paragem de réplica não primária antes da transição estar concluída. Isto também pode descobrir problemas em que tal processo bloqueia uma mudança de função ou pedido de encerramento da Service Fabric de completar.

#### <a name="verify-which-replicas-should-serve-requests"></a>Verifique quais réplicas devem servir pedidos
Os testes devem afirmar o comportamento esperado se um pedido for encaminhado para uma réplica não primária. O Service Fabric fornece a capacidade de ter réplicas secundárias que servem pedidos. No entanto, as gravações para coleções fiáveis só podem ocorrer a partir da réplica primária. Se a sua aplicação pretender apenas réplicas primárias para servir pedidos ou, apenas um subconjunto de pedidos pode ser tratado por um secundário, então os testes devem afirmar o comportamento esperado tanto para os casos positivos como negativos. O caso negativo sendo um pedido é encaminhado para uma réplica que não deve lidar com o pedido e, sendo o positivo o oposto.

## <a name="next-steps"></a>Passos seguintes
Saiba como [equipar os serviços estatais de teste](service-fabric-how-to-unit-test-stateful-services.md).
