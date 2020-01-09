---
title: Solucionar problemas ao usar o gatilho de Azure Functions para Cosmos DB
description: Problemas comuns, soluções alternativas e etapas de diagnóstico, ao usar o gatilho de Azure Functions para Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.date: 07/17/2019
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: f3af350c96d1dd9eaf4773db503acb10d8a08a8f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441123"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-functions-trigger-for-cosmos-db"></a>Diagnosticar e solucionar problemas ao usar o gatilho de Azure Functions para Cosmos DB

Este artigo aborda problemas comuns, soluções alternativas e etapas de diagnóstico, quando você usa o [gatilho de Azure Functions para Cosmos DB](change-feed-functions.md).

## <a name="dependencies"></a>Dependências

O gatilho Azure Functions e as associações para Cosmos DB dependem dos pacotes de extensão no tempo de execução de Azure Functions base. Sempre mantenha esses pacotes atualizados, pois eles podem incluir correções e novos recursos que podem solucionar possíveis problemas encontrados:

* Para Azure Functions v2, consulte [Microsoft. Azure. webjobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB).
* Para Azure Functions v1, consulte [Microsoft. Azure. webjobs. Extensions. DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB).

Este artigo sempre fará referência a Azure Functions v2 sempre que o tempo de execução for mencionado, a menos que seja especificado explicitamente.

## <a name="consume-the-azure-cosmos-db-sdk-independently"></a>Consumir o SDK do Azure Cosmos DB de forma independente

A principal funcionalidade do pacote de extensão é fornecer suporte para o gatilho de Azure Functions e associações para Cosmos DB. Ele também inclui o [SDK do .net Azure Cosmos DB](sql-api-sdk-dotnet-core.md), que é útil se você deseja interagir com Azure Cosmos DB programaticamente sem usar o gatilho e as associações.

Se quiser usar o SDK do Azure Cosmos DB, certifique-se de não adicionar ao seu projeto outra referência de pacote NuGet. Em vez disso, **permita que a referência do SDK seja resolvida por meio do pacote de extensão do Azure Functions**. Consumir o SDK do Azure Cosmos DB separadamente do gatilho e das associações

Além disso, se você estiver criando manualmente sua própria instância do [cliente SDK do Azure Cosmos DB](./sql-api-sdk-dotnet-core.md), deverá seguir o padrão de ter apenas uma instância do cliente [usando uma abordagem de padrão singleton](../azure-functions/manage-connections.md#documentclient-code-example-c). Esse processo evitará os problemas de soquete em potencial em suas operações.

## <a name="common-scenarios-and-workarounds"></a>Cenários comuns e soluções alternativas

### <a name="azure-function-fails-with-error-message-collection-doesnt-exist"></a>A função do Azure falha com a coleta de mensagens de erro não existe

A função do Azure falha com a mensagem de erro "a coleção de origem ' Collection-Name ' (no banco de dados ' Database-Name ') ou a coleção de concessão ' Collection2-Name ' (no banco de dados ' Database2-Name ') não existe. Ambas as coleções devem existir antes de o ouvinte ser iniciado. Para criar automaticamente a coleção de concessão, defina ' CreateLeaseCollectionIfNotExists ' como ' true ' "

Isso significa que um ou ambos os contêineres Cosmos do Azure necessários para o gatilho funcionar não existem ou não estão acessíveis para a função do Azure. **O erro em si informará qual contêiner e banco de dados Cosmos do Azure é o gatilho procurando** com base em sua configuração.

1. Verifique o atributo `ConnectionStringSetting` e se ele **faz referência a uma configuração que existe em seu aplicativo de funções do Azure**. O valor nesse atributo não deve ser a própria cadeia de conexão, mas o nome do parâmetro de configuração.
2. Verifique se as `databaseName` e `collectionName` existem na sua conta do cosmos do Azure. Se você estiver usando a substituição automática de valor (usando padrões de `%settingName%`), verifique se o nome da configuração existe em seu Aplicativo de funções do Azure.
3. Se você não especificar um `LeaseCollectionName/leaseCollectionName`, o padrão será "concessões". Verifique se esse contêiner existe. Opcionalmente, você pode definir o atributo `CreateLeaseCollectionIfNotExists` em seu gatilho para `true` para criá-lo automaticamente.
4. Verifique a [configuração de firewall da sua conta do Azure Cosmos](how-to-configure-firewall.md) para ver se não está bloqueando a função do Azure.

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>Falha ao iniciar a função do Azure com "a coleção de produtividade compartilhada deve ter uma chave de partição"

As versões anteriores da extensão de Azure Cosmos DB não tinham suporte usando um contêiner de concessões que foi criado em um [banco de dados de produtividade compartilhado](./set-throughput.md#set-throughput-on-a-database). Para resolver esse problema, atualize a extensão [Microsoft. Azure. webjobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) para obter a versão mais recente.

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>Falha na inicialização da função do Azure com "a coleção de concessão, se particionada, deve ter a chave de partição igual à ID".

Esse erro significa que o contêiner de concessões atuais está particionado, mas o caminho da chave de partição não é `/id`. Para resolver esse problema, você precisa recriar o contêiner de concessões com `/id` como a chave de partição.

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>Você verá um "o valor não pode ser nulo. Nome do parâmetro: o "em seus logs de Azure Functions quando você tenta executar o gatilho

Esse problema será exibido se você estiver usando o portal do Azure e tentar selecionar o botão **executar** na tela ao inspecionar uma função do Azure que usa o gatilho. O gatilho não exige que você selecione executar para iniciar, ele será iniciado automaticamente quando a função do Azure for implantada. Se você quiser verificar o fluxo de log da função do Azure na portal do Azure, basta acessar o contêiner monitorado e inserir alguns novos itens, você verá automaticamente o gatilho em execução.

### <a name="my-changes-take-too-long-to-be-received"></a>Minhas alterações demoram muito para serem recebidas

Esse cenário pode ter várias causas e todas elas devem ser verificadas:

1. A Função do Azure está implementada na mesma região que a sua conta do Azure Cosmos? Para obter uma latência de rede ideal, tanto a Função do Azure como a sua conta do Azure Cosmos devem estar colocadas na mesma região do Azure.
2. As alterações que ocorrem no contentor do Azure Cosmos são contínuas ou esporádicas?
Se for a última, poderá existir algum atraso entre as alterações a serem armazenadas e a Função do Azure que as seleciona. Tal ocorre porque, internamente, quando o acionador verifica a existência de alterações no contentor do Azure Cosmos e não encontra nenhuma pendente para ser lida, este entra no modo de suspensão durante um período de tempo configurável (5 segundos, por predefinição) antes de verificar a existência de novas alterações (para evitar um consumo elevado de RUs). Pode configurar este tempo de suspensão através da definição `FeedPollDelay/feedPollDelay` na [configuração](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration) do acionador (espera-se que o valor esteja em milissegundos).
3. O contêiner Cosmos do Azure pode ser [limitado por taxa](./request-units.md).
4. Você pode usar o atributo `PreferredLocations` em seu gatilho para especificar uma lista separada por vírgulas de regiões do Azure para definir uma ordem de conexão preferencial personalizada.

### <a name="some-changes-are-missing-in-my-trigger"></a>Algumas alterações estão ausentes no meu gatilho

Se você descobrir que algumas das alterações que ocorreram em seu contêiner Cosmos do Azure não estão sendo coletadas pela função do Azure, há uma etapa de investigação inicial que precisa ocorrer.

Quando sua função do Azure recebe as alterações, ele geralmente as processa e, opcionalmente, envia o resultado para outro destino. Quando você estiver investigando alterações ausentes, certifique-se de **que você meça quais alterações estão sendo recebidas no ponto de ingestão** (quando a função do Azure é iniciada), não no destino.

Se algumas alterações estiverem ausentes no destino, isso pode significar que o erro ocorre durante a execução da função do Azure depois que as alterações foram recebidas.

Nesse cenário, o melhor curso de ação é adicionar blocos de `try/catch` em seu código e dentro dos loops que podem estar processando as alterações, para detectar qualquer falha de um determinado subconjunto de itens e tratá-los de forma adequada (enviá-los para outro armazenamento para análise ou repetição posterior). 

> [!NOTE]
> Por padrão, o gatilho de Azure Functions para Cosmos DB não repetirá um lote de alterações se houver uma exceção sem tratamento durante a execução do código. Isso significa que o motivo pelo qual as alterações não chegaram no destino é porque você está falhando em processá-las.

Se você descobrir que algumas alterações não foram recebidas por seu gatilho, o cenário mais comum é que há **outra função do Azure em execução**. Pode ser outra função do Azure implantada no Azure ou uma função do Azure em execução localmente na máquina de um desenvolvedor que tenha **exatamente a mesma configuração** (mesmo contêineres monitorados e de concessão), e essa função do Azure está roubando um subconjunto das alterações que você esperaria que sua função do Azure processasse.

Além disso, o cenário pode ser validado, se você souber quantas instâncias do Azure Aplicativo de funções você está executando. Se você inspecionar o contêiner de concessões e contar o número de itens de concessão no, os valores distintos da propriedade `Owner` neles contidos deverão ser iguais ao número de instâncias do seu Aplicativo de funções. Se houver mais Proprietários do que as instâncias conhecidas da Aplicação de Funções do Azure, tal significa que estes proprietários adicionais serão os responsáveis pelo “roubo” das alterações.

Uma maneira fácil de solucionar essa situação é aplicar um `LeaseCollectionPrefix/leaseCollectionPrefix` à sua função com um valor novo/diferente ou, como alternativa, testar com um novo contêiner de concessões.

### <a name="need-to-restart-and-re-process-all-the-items-in-my-container-from-the-beginning"></a>É necessário reiniciar e processar novamente todos os itens no meu contêiner a partir do início 
Para processar novamente todos os itens em um contêiner desde o início:
1. Pare sua função do Azure se ela estiver em execução no momento. 
1. Excluir os documentos na coleção de concessão (ou excluir e recriar a coleção de concessão para que ela fique vazia)
1. Defina o atributo [StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration) CosmosDBTrigger em sua função como true. 
1. Reinicie o Azure function. Agora, ele lerá e processará todas as alterações desde o início. 

Definir [StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration) como true instruirá a função do Azure a começar a ler as alterações desde o início do histórico da coleção, em vez da hora atual. Isso só funciona quando não há concessões já criadas (ou seja, documentos na coleção de concessões). Definir essa propriedade como true quando houver concessões já criadas não tem efeito; Nesse cenário, quando uma função é interrompida e reiniciada, ela começa a ler do último ponto de verificação, conforme definido na coleção de concessões. Para reprocessar desde o início, siga as etapas acima de 1-4.  

### <a name="binding-can-only-be-done-with-ireadonlylistdocument-or-jarray"></a>A associação só pode ser feita com IReadOnlyList\<documento > ou JArray

Esse erro ocorrerá se seu projeto Azure Functions (ou qualquer projeto referenciado) contiver uma referência manual do NuGet para o SDK do Azure Cosmos DB com uma versão diferente daquela fornecida pela [extensão Azure Functions Cosmos DB](./troubleshoot-changefeed-functions.md#dependencies).

Para solucionar essa situação, remova a referência manual do NuGet que foi adicionada e permita que o Azure Cosmos DB referência do SDK seja resolvido por meio do pacote de extensão Azure Functions Cosmos DB.

### <a name="changing-azure-functions-polling-interval-for-the-detecting-changes"></a>Alterando o intervalo de sondagem da função do Azure para a detecção de alterações

Conforme explicado anteriormente para [que minhas alterações demore muito para serem recebidas, a](./troubleshoot-changefeed-functions.md#my-changes-take-too-long-to-be-received)função do Azure entrará em suspensão por um período configurável (5 segundos, por padrão) antes de verificar se há novas alterações (para evitar o alto consumo de ru). Pode configurar este tempo de suspensão através da definição `FeedPollDelay/feedPollDelay` na [configuração](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration) do acionador (espera-se que o valor esteja em milissegundos).

## <a name="next-steps"></a>Passos seguintes

* [Habilitar o monitoramento para seu Azure Functions](../azure-functions/functions-monitoring.md)
* [Solução de problemas do SDK do .NET Azure Cosmos DB](./troubleshoot-dot-net-sdk.md)
