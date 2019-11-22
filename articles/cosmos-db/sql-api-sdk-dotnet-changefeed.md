---
title: 'Azure Cosmos DB: API do processador do feed de alterações do .NET, recursos do SDK &'
description: Saiba tudo sobre a API do processador do feed de alterações e o SDK, incluindo datas de lançamento, datas de desativação e alterações feitas entre cada versão do SDK do processador do feed de alterações do .NET.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 01/30/2019
ms.author: maquaran
ms.openlocfilehash: bd727e9c68a34c8e182a6726b257a6bf37178837
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74306767"
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>SDK do processador do feed de alterações do .NET: download e notas de versão

> [!div class="op_single_selector"]
>
> * [.NET](sql-api-sdk-dotnet.md)
> * [Feed de alterações de .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Fornecedor de Recursos REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Executor em massa-.NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Executor em massa – Java](sql-api-sdk-bulk-executor-java.md)

|   |   |
|---|---|
|**Transferência de SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**Documentação da API**|[Documentação de referência da API da biblioteca do processador de feed de alterações](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**Introdução**|[Introdução ao SDK do .NET do processador do feed de alterações](change-feed.md)|
|**Estrutura de suporte atual**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Núcleo de Microsoft .NET](https://www.microsoft.com/net/download/core) |

> [!NOTE]
> Se você estiver usando o processador do feed de alterações, consulte a versão mais recente 3. x do [SDK do .net](change-feed-processor.md), que tem o feed de alterações interno do SDK. 

## <a name="release-notes"></a>Notas de versão

### <a name="v2-builds"></a>compilações v2

### <a name="a-name228228"></a><a name="2.2.8"/>2.2.8
* Aprimoramentos de estabilidade e diagnóstico:
  * Suporte adicionado para detectar a leitura do feed de alterações demorando muito. Quando demora mais do que o valor especificado pela propriedade `ChangeFeedProcessorOptions.ChangeFeedTimeout`, as etapas a seguir são executadas:
    * A operação para ler o feed de alterações na partição problemática foi anulada.
    * A instância do processador do feed de alterações elimina a propriedade da concessão problemática. A concessão descartada será coletada durante a próxima etapa de aquisição de concessão que será feita pela mesma instância do processador do feed de alterações ou diferente. Dessa forma, a leitura do feed de alterações será reiniciada.
    * Um problema é relatado no Health Monitor. O monitor integridade padrão envia todos os problemas relatados para o log de rastreamento.
  * Adicionada uma nova propriedade pública: `ChangeFeedProcessorOptions.ChangeFeedTimeout`. O valor padrão dessa propriedade é 10 minutos.
  * Adicionado um novo valor de enumeração público: `Monitoring.MonitoredOperation.ReadChangeFeed`. Quando o valor de `HealthMonitoringRecord.Operation` é definido como `Monitoring.MonitoredOperation.ReadChangeFeed`, ele indica que o problema de integridade está relacionado à leitura do feed de alterações.

### <a name="a-name227227"></a><a name="2.2.7"/>2.2.7
* Melhor estratégia de balanceamento de carga para o cenário ao obter todas as concessões leva mais tempo do que o intervalo de expiração de concessão, por exemplo, devido a problemas de rede:
  * Nesse cenário, o algoritmo de balanceamento de carga usado para considerar de forma falsa as concessões expiradas, causando o roubo de concessões de proprietários ativos. Isso poderia disparar um novo balanceamento desnecessário de muitas concessões.
  * Esse problema é corrigido nesta versão, evitando a repetição em conflito ao adquirir a concessão expirada cujo proprietário não foi alterado e posponing adquirir a concessão expirada para a próxima iteração de balanceamento de carga.

### <a name="a-name226226"></a><a name="2.2.6"/>2.2.6
* Tratamento aprimorado de exceções de observador.
* Informações mais detalhadas sobre erros de observador:
  * Quando um observador é fechado devido a uma exceção gerada pelo ProcessChangesAsync do observador, o CloseAsync agora receberá o parâmetro Reason definido como ChangeFeedObserverCloseReason. ObserverError.
  * Rastreamentos adicionados para identificar erros no código do usuário em um observador.

### <a name="a-name225225"></a><a name="2.2.5"/>2.2.5
* Adicionado suporte para manipulação de divisão em coleções que usam taxa de transferência de banco de dados compartilhado.
  * Esta versão corrige um problema que pode ocorrer durante a divisão em coleções usando a taxa de transferência do banco de dados compartilhado quando dividir o resultado na partição rebalanceando apenas um intervalo de chaves de partição filho criado, em vez de dois. Quando isso acontece, o processador do feed de alterações pode ficar preso excluindo a concessão de um intervalo de chaves de partição antiga e não criando novas concessões. O problema foi corrigido nesta versão.

### <a name="a-name224224"></a><a name="2.2.4"/>2.2.4
* Adicionada nova propriedade ChangeFeedProcessorOptions. StartContinuation para dar suporte ao início do feed de alterações a partir do token de continuação de solicitação. Isso só é usado quando a coleta de concessão está vazia ou uma concessão não tem ContinuationToken definido. Para concessões na coleção de concessão que têm ContinuationToken definido, o ContinuationToken é usado e ChangeFeedProcessorOptions. StartContinuation é ignorado.

### <a name="a-name223223"></a><a name="2.2.3"/>2.2.3
* Suporte adicionado para usar o repositório personalizado para persistir tokens de continuação por partição.
  * Por exemplo, um repositório de concessão personalizado pode ser Azure Cosmos DB coleção de concessão particionada de qualquer forma personalizada.
  * Os repositórios de concessão personalizados podem usar o novo ponto de extensibilidade ChangeFeedProcessorBuilder. WithLeaseStoreManager (ILeaseStoreManager) e a interface pública ILeaseStoreManager.
  * Refatorar a interface ILeaseManager em várias interfaces de função.
* Alteração significativa de quebra: o ponto de extensibilidade removido ChangeFeedProcessorBuilder. WithLeaseManager (ILeaseManager), use ChangeFeedProcessorBuilder. WithLeaseStoreManager (ILeaseStoreManager) em vez disso.

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2
* Esta versão corrige um problema que ocorre durante o processamento de uma divisão na coleção monitorada e usando uma coleção de concessão particionada. Ao processar uma concessão para a partição de divisão, a concessão correspondente a essa partição pode não ser excluída. O problema foi corrigido nesta versão.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* Corrigido o cálculo do estimador para contas de vários mestres e o novo formato de token de sessão.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Suporte adicionado para coleções de concessão particionadas. A chave de partição deve ser definida como/ID.
* Alteração significativa de quebra: os métodos da interface IChangeFeedDocumentClient e da classe ChangeFeedDocumentClient foram alterados para incluir os parâmetros requestoptions e CancellationToken. IChangeFeedDocumentClient é um ponto de extensibilidade avançado que permite que você forneça a implementação personalizada do cliente de documento para usar com o processador do feed de alterações, por exemplo, decorar o DocumentClient e interceptar todas as chamadas para ele para fazer rastreamento extra, tratamento de erros diante. Com essa atualização, o código que implementa IChangeFeedDocumentClient precisará ser alterado para incluir novos parâmetros na implementação.
* Melhorias de diagnóstico secundárias.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Adição da nova API, tarefa&lt;IReadOnlyList&lt;RemainingPartitionWork&gt;&gt; IRemainingWorkEstimator. GetEstimatedRemainingWorkPerPartitionAsync (). Isso pode ser usado para obter o trabalho estimado para cada partição.
* Dá suporte ao SDK do Microsoft. Azure. DocumentDB 2,0. Requer Microsoft. Azure. DocumentDB 2,0 ou posterior.

### <a name="a-name206206"></a><a name="2.0.6"/>2.0.6
* Adicionada a propriedade pública ChangeFeedEventHost. HostName para compatibilidade com v1.

### <a name="a-name205205"></a><a name="2.0.5"/>2.0.5
* Correção de uma condição de corrida que ocorre durante a divisão da partição. A condição de corrida pode levar à aquisição da concessão e à sua perda imediata durante a divisão da partição e à causa da contenção. O problema da condição de corrida é corrigido com esta versão.

### <a name="a-name204204"></a><a name="2.0.4"/>2.0.4
* SDK DE DISPONIBILIDADE GERAL

### <a name="a-name203-prerelease203-prerelease"></a><a name="2.0.3-prerelease"/>2.0.3-prerelease
* Foram corrigidos os problemas seguintes:
  * Quando a divisão de partição acontece, pode haver processamento duplicado de documentos modificados antes da divisão.
  * A API GetEstimatedRemainingWork retornou 0 quando nenhuma concessão estava presente na coleção de concessão.

* As seguintes exceções se tornam públicas. As extensões que implementam IPartitionProcessor podem gerar essas exceções.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.LeaseLostException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionNotFoundException.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionSplitException. 

### <a name="a-name202-prerelease202-prerelease"></a><a name="2.0.2-prerelease"/>2.0.2-prerelease
* Alterações secundárias da API:
  * Removeu ChangeFeedProcessorOptions. IsAutoCheckpointEnabled que foi marcado como obsoleto.

### <a name="a-name201-prerelease201-prerelease"></a><a name="2.0.1-prerelease"/>2.0.1-prerelease
* Aprimoramentos de estabilidade:
  * Melhor manipulação da inicialização do armazenamento de concessão. Quando o repositório de concessão está vazio, apenas uma instância do processador pode inicializá-lo, os outros esperam.
  * Renovação/lançamento de concessão mais estável/eficiente. A renovação e a liberação de uma partição de concessão de uma só é independente da renovação de outras. Em v1, que foi feito sequencialmente para todas as partições.
* Nova API v2:
  * Padrão de construtor para a construção flexível do processador: a classe ChangeFeedProcessorBuilder.
    * Pode usar qualquer combinação de parâmetros.
    * Pode usar a instância DocumentClient para monitoramento e/ou coleta de concessão (não disponível em v1).
  * IChangeFeedObserver. ProcessChangesAsync agora leva CancellationToken.
  * IRemainingWorkEstimator-o estimador de trabalho restante pode ser usado separadamente do processador.
  * Novos pontos de extensibilidade:
    * IPartitionLoadBalancingStrategy-para o balanceamento de carga personalizado de partições entre instâncias do processador.
    * ILease, ILeaseManager-para gerenciamento de concessão personalizado.
    * IPartitionProcessor-para alterações de processamento personalizado em uma partição.
* Logging-usa a biblioteca [LibLog](https://github.com/damianh/LibLog) .
* 100% compatível com versões anteriores com a API v1.
* Nova base de código.
* Compatível com o [SQL .NET SDK](sql-api-sdk-dotnet.md) versões 1.21.1 e posteriores.

### <a name="v1-builds"></a>compilações v1

### <a name="a-name133133"></a><a name="1.3.3"/>1.3.3
* Adição de mais registro em log.
* Corrigido um vazamento de DocumentClient ao chamar a estimativa de trabalho pendente várias vezes.

### <a name="a-name132132"></a><a name="1.3.2"/>1.3.2
* Correções na estimativa de trabalho pendente.

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1
* Aprimoramentos de estabilidade.
  * Correção para lidar com o problema de tarefas canceladas que podem levar a observadores interrompidos em algumas partições.
* Suporte para ponto de verificação manual.
* Compatível com o [SQL .NET SDK](sql-api-sdk-dotnet.md) versões 1,21 e posteriores.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Adiciona suporte para .NET Standard 2,0. O pacote agora dá suporte a monikers `netstandard2.0` e `net451` Framework.
* Compatível com o [SQL .NET SDK](sql-api-sdk-dotnet.md) versões 1.17.0 e posteriores.
* Compatível com o [SQL SDK do .NET Core](sql-api-sdk-dotnet-core.md) versões 1.5.1 e posteriores.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1
* Corrige um problema com o cálculo da estimativa do trabalho restante quando o feed de alterações estava vazio ou nenhum trabalho estava pendente.
* Compatível com o [SQL .NET SDK](sql-api-sdk-dotnet.md) versões 1.13.2 e posteriores.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Um método foi adicionado para obter uma estimativa do trabalho restante a ser processado no feed de alterações.
* Compatível com o [SQL .NET SDK](sql-api-sdk-dotnet.md) versões 1.13.2 e posteriores.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* SDK DE DISPONIBILIDADE GERAL
* Compatível com as versões 1.14.1 e abaixo do [SDK do SQL .net](sql-api-sdk-dotnet.md) .

## <a name="release--retirement-dates"></a>Datas de lançamento & desativação

A Microsoft irá fornecer, pelo menos, notificação **12 meses** antecedência extinguir um SDK para facilitar a transição para uma versão mais recente/suportadas.

Novos recursos e funcionalidades e otimizações são adicionados somente ao SDK atual, por isso é recomendável que você sempre atualize para a versão mais recente do SDK o mais cedo possível. 

Qualquer pedido ao Cosmos DB com um SDK extinto será rejeitado pelo serviço.

<br/>

| Versão | Data de lançamento | Data de retirada |
| --- | --- | --- |
| [2.2.8](#2.2.8) |28 de outubro de 2019 |--- |
| [2.2.7](#2.2.7) |14 de maio de 2019 |--- |
| [2.2.6](#2.2.6) |29 de janeiro de 2019 |--- |
| [2.2.5](#2.2.5) |13 de dezembro de 2018 |--- |
| [2.2.4](#2.2.4) |29 de novembro de 2018 |--- |
| [2.2.3](#2.2.3) |19 de novembro de 2018 |--- |
| [2.2.2](#2.2.2) |31 de outubro de 2018 |--- |
| [2.2.1](#2.2.1) |24 de outubro de 2018 |--- |
| [1.3.3](#1.3.3) |08 de maio de 2018 |--- |
| [1.3.2](#1.3.2) |18 de abril de 2018 |--- |
| [1.3.1](#1.3.1) |13 de março de 2018 |--- |
| [1.2.0](#1.2.0) |31 de outubro de 2017 |--- |
| [1.1.1](#1.1.1) |29 de agosto de 2017 |--- |
| [1.1.0](#1.1.0) |13 de agosto de 2017 |--- |
| [1.0.0](#1.0.0) |07 de julho de 2017 |--- |

## <a name="faq"></a>FAQ

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Consulte também

Para saber mais sobre o Cosmos DB, veja [do Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) página do serviço.
