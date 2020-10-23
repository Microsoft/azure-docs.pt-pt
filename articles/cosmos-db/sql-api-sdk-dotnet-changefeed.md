---
title: Notas de versão da API e SDK de Processador de Feed de Alterações .NET do Azure Cosmos DB
description: Saiba tudo sobre a API e SDK Processador de Feed de Alterações, incluindo datas de versões, datas de descontinuação e as alterar feitas entre cada versão do SDK Processador de Feed de Alterações .NET.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.openlocfilehash: e4c2969db560ff20cae2ed7b9ffbe0cea206c7a1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91611576"
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>SDK Processador de Feed de Alterações .NET: Transferência e notas de versão

> [!div class="op_single_selector"]
>
> * [SDK .NET v3](sql-api-sdk-dotnet-standard.md)
> * [SDK .NET v2](sql-api-sdk-dotnet.md)
> * [SDK v2 de .NET Core](sql-api-sdk-dotnet-core.md)
> * [SDK Feed de Alterações .NET v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [SDK v4 de Java](sql-api-sdk-java-v4.md)
> * [SDK v2 Java assíncrono](sql-api-sdk-async-java.md)
> * [SDK v2 Java síncrono](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark Connector](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api
> * [REST Resource Provider](/rest/api
> * [SQL](sql-api-query-reference.md)
> * [Executor em massa - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Executor em massa - Java](sql-api-sdk-bulk-executor-java.md)

|   |   |
|---|---|
|**Transferência de SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**Documentação da API**|[Documentação de referência da API da biblioteca do Processador de Feed de Alterações](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet&preserve-view=true)|
|**Introdução**|[Introdução ao SDK Processador de Feed de Alterações .NET](change-feed.md)|
|**Framework suportado atualmente**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

> [!NOTE]
> Se estiver a utilizar o processador de feed de alterações, veja a versão 3.x mais recente do [SDK .NET](change-feed-processor.md), que tem o feed de alterações incorporado no SDK. 

## <a name="release-notes"></a>Notas de versão

### <a name="v2-builds"></a>Compilações v2

### <a name="232"></a><a id="2.3.2"></a>2.3.2
* Adicionada compatibilidade de armazenamento de concessão com o SDK [V3 que permite caminhos de migração em direto. Uma aplicação pode ser migrada para o SDK V3 e migrada de novo para a biblioteca do Processador de Feed de Alterações sem perder qualquer estado.

### <a name="231"></a><a id="2.3.1"></a>2.3.1
* Corrigido um caso em que o motivo de fecho `FeedProcessing.ChangeFeedObserverCloseReason.Unknown` era enviado para `FeedProcessing.IChangeFeedObserver.CloseAsync` se a partição não fosse encontrada ou se a réplica de destino não estivesse atualizada com a sessão de leitura. Nestes casos, são agora utilizados os motivos de fecho `FeedProcessing.ChangeFeedObserverCloseReason.ResourceGone` e `FeedProcessing.ChangeFeedObserverCloseReason.ReadSessionNotAvailable`.
* Adicionado um novo motivo de fecho, `FeedProcessing.ChangeFeedObserverCloseReason.ReadSessionNotAvailable`, que é enviado para fechar o observador do feed de alterações quando a réplica de destino não está atualizada com a sessão de leitura.

### <a name="230"></a><a id="2.3.0"></a>2.3.0
* Adicionado um novo método `ChangeFeedProcessorBuilder.WithCheckpointPartitionProcessorFactory` e a interface pública correspondente `ICheckpointPartitionProcessorFactory`. Permite uma implementação da interface `IPartitionProcessor` para utilizar o mecanismo de pontos de verificação incorporado. A nova fábrica é semelhante à existente, `IPartitionProcessorFactory`, exceto que o respetivo método `Create` também assume o parâmetro `ILeaseCheckpointer`.
* Só pode ser utilizado um dos dois métodos, `ChangeFeedProcessorBuilder.WithPartitionProcessorFactory` ou `ChangeFeedProcessorBuilder.WithCheckpointPartitionProcessorFactory`, para a mesma instância `ChangeFeedProcessorBuilder`.

### <a name="228"></a><a id="2.2.8"></a>2.2.8
* Melhorias à estabilidade e ao diagnóstico:
  * Adicionado suporte para detetar leituras do feed de alterações que demoram muito tempo. Quando demora mais tempo do que o valor especificado pela propriedade `ChangeFeedProcessorOptions.ChangeFeedTimeout`, são realizados os seguintes passos:
    * A operação para ler o feed de alterações na partição problemática é abortada.
    * A instância do processador do feed de alterações elimina a propriedade da concessão problemática. A concessão eliminada será retomada durante o próximo passo de aquisição da concessão, o qual será feito pela mesma ou por outra instância do processador de feed de alterações. Desta forma, a leitura do feed de alterações é recomeçada.
    * É reportado um problema ao monitor de estado de funcionamento. O monitor de estado de funcionamento predefinido envia todos os problemas reportados para o registo de rastreio.
  * Adicionada uma propriedade pública nova, `ChangeFeedProcessorOptions.ChangeFeedTimeout`. O valor predefinido desta propriedade é 10 minutos.
  * Adicionado um valor de enumeração pública novo, `Monitoring.MonitoredOperation.ReadChangeFeed`. Quando o valor de `HealthMonitoringRecord.Operation` está definido como `Monitoring.MonitoredOperation.ReadChangeFeed`, indica que o problema de estado de funcionamento está relacionado com a leitura do feed de alterações.

### <a name="227"></a><a id="2.2.7"></a>2.2.7
* Melhorada a estratégia de balanceamento de carga para o cenário em que a obtenção de todas as concessões demora mais tempo do que o intervalo de validade da concessão; por exemplo, devido a problemas de rede.
  * Neste cenário, é utilizado o algoritmo de balanceamento de carga para considerar, falsamente, as concessões como expiradas, fazendo com que os proprietários ativos percam as concessões. Esta situação pode originar a repetição desnecessária do balanceamento de muitas concessões.
  * Este problema foi corrigido nesta versão ao evitar a repetição em caso de conflito ao adquirir a concessão expirada, cujo proprietário não mudou, e ao adiar a aquisição da concessão expirada para a próxima iteração do balanceamento de carga.

### <a name="226"></a><a id="2.2.6"></a>2.2.6
* Processamento das exceções do observador melhorado.
* Informações mais ricas sobre os erros do observador:
  * Quando um observador é fechado devido a uma exceção emitida pelo respetivo ProcessChangesAsync, CloseAsync receberá agora o parâmetro de motivo definido como ChangeFeedObserverCloseReason.ObserverError.
  * Adicionados rastreios para identificar erros no código do utilizador num observador.

### <a name="225"></a><a id="2.2.5"></a>2.2.5
* Adicionado suporte para lidar com a divisão em coleções que utilizam o débito de bases de dados partilhado.
  * Esta versão corrige um problema que pode ocorrer durante a divisão em coleções que utilizem o débito de bases de dados partilhado quando a divisão resulta num novo balanceamento da partição com apenas um intervalo de chaves de partições subordinadas criadas, em vez de dois. Quando isto acontece, o Processador de Feed de Alterações pode ficar bloqueado ao eliminar a concessão do intervalo de chaves de partições antigo e não criar concessões novas. O problema está corrigido nesta versão.

### <a name="224"></a><a id="2.2.4"></a>2.2.4
* Foi adicionada a propriedade nova ChangeFeedProcessorOptions.StartContinuation para suportar o início do feed de alterações a partir do token de continuação de pedido. Só é utilizado quando a coleção de concessões está vazia ou quando uma concessão não tem ContinuationToken definido. Relativamente às concessões na coleção de concessões que têm ContinuationToken definido, este é utilizado e ChangeFeedProcessorOptions.StartContinuation ignorado.

### <a name="223"></a><a id="2.2.3"></a>2.2.3
* Adicionado suporte para a utilização de arquivo personalizado para persistir os tokens de continuação por partição.
  * Por exemplo, um arquivo de concessões personalizado pode ser particionado em coleções do Azure Cosmos DB de qualquer forma personalizada.
  * Os arquivos de concessões personalizados podem utilizar o novo ponto de extensibilidade ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager) e a interface pública ILeaseStoreManager.
  * A interface ILeaseManager foi refatorizada em várias interfaces de funções.
* Alteração interruptiva menor: foi removido o ponto de extensibilidade ChangeFeedProcessorBuilder.WithLeaseManager(ILeaseManager); em alternativa, utilize ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager).

### <a name="222"></a><a id="2.2.2"></a>2.2.2
* Esta versão corrige um problema que ocorre durante o processamento de uma divisão na coleção monitorizada quando é utilizada uma coleção de concessões particionada. Ao processar uma concessão para partição de divisão, a concessão correspondente a essa partição pode não ser eliminada. O problema está corrigido nesta versão.

### <a name="221"></a><a id="2.2.1"></a>2.2.1
* Foi corrigido o estimador de cálculo para contas com múltiplas regiões de escrita e um novo formato de Token de Sessão.

### <a name="220"></a><a id="2.2.0"></a>2.2.0
* Adicionado suporte para coleções de concessões particionadas. A chave de partição tem de ser definida como /id.
* Alteração interruptiva menor: os métodos da interface IChangeFeedDocumentClient e da classe ChangeFeedDocumentClient foram alterados para passar a incluir os parâmetros RequestOptions e CancellationToken. IChangeFeedDocumentClient é um ponto de extensibilidade avançado que lhe permite fornecer uma implementação personalizada do Cliente de Documento a utilizar com o Processador de Feed de Alterações; por exemplo, decorar DocumentClient e intercetar todas as chamadas ao mesmo para realizar rastreios, processamento de erros extra, etc. Com esta atualização, o código que implementa IChangeFeedDocumentClient tem de ser alterado para incluir os parâmetros novos na implementação.
* Melhoras menores ao diagnóstico.

### <a name="210"></a><a id="2.1.0"></a>2.1.0
* Adicionado uma API nova, Task&lt;IReadOnlyList&lt;RemainingPartitionWork&gt;&gt; IRemainingWorkEstimator.GetEstimatedRemainingWorkPerPartitionAsync(). Pode ser utilizada para obter o trabalho estimado para cada partição.
* Suporta o SDK Microsoft.Azure.DocumentDB 2.0 Requer o Microsoft.Azure.DocumentDB 2.0 ou posterior.

### <a name="206"></a><a id="2.0.6"></a>2.0.6
* Adicionada a propriedade pública ChangeFeedEventHost.HostName para compatibilidade com v1.

### <a name="205"></a><a id="2.0.5"></a>2.0.5
* Corrigida uma condição race que ocorre durante a divisão da partição. A condição race pode levar à aquisição da concessão e à perda imediata da mesma durante a divisão da partição, provocando contenção. O problema da condição race está corrigido nesta versão.

### <a name="204"></a><a id="2.0.4"></a>2.0.4
* SDK em Disponibilidade Geral

### <a name="203-prerelease"></a><a id="2.0.3-prerelease"></a>2.0.3-prerelease
* Foram corrigidos os problemas seguintes:
  * Quando ocorre a divisão da partição, pode haver um processamento duplicado dos documentos modificados antes da divisão.
  * A API GetEstimatedRemainingWork devolveu 0 quando não estavam presentes quaisquer concessões na coleção de concessões.

* As exceções seguintes são tornadas públicas. Extensões que implementam IPartitionProcessor podem emitir estas exceções.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.LeaseLostException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionNotFoundException.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionSplitException. 

### <a name="202-prerelease"></a><a id="2.0.2-prerelease"></a>2.0.2-prerelease
* Alterações menores à API:
  * ChangeFeedProcessorOptions.IsAutoCheckpointEnabled foi removido, porque foi marcado como obsoleto.

### <a name="201-prerelease"></a><a id="2.0.1-prerelease"></a>2.0.1-prerelease
* Melhorias à estabilidade:
  * Melhor processamento da inicialização do arquivo de concessões. Quando o arquivo de concessões está vazio, só uma instância do processador o pode inicializar; os outros aguardam.
  * Renovação/lançamento de concessões mais estável/eficiente. A renovação e o lançamento de uma partição de concessão são independentes da renovação de outras. Na v1, isto era feito sequencialmente para todas as partições.
* Nova API v2:
  * Padrão de construtor para construção flexível do processador: a classe ChangeFeedProcessorBuilder.
    * Pode assumir qualquer combinação de parâmetros.
    * Pode assumir a instância DocumentClient para monitorização e/ou recolha de concessão (não disponível na v1).
  * IChangeFeedObserver.ProcessChangesAsync tem agora CancellationToken.
  * IRemainingWorkEstimator - o estimador de trabalho restante pode ser utilizado em separado do processador.
  * Novos pontos de extensibilidade:
    * IPartitionLoadBalancingStrategy - para balanceamento de carga personalizado de partições entre instâncias do processador.
    * ILease, ILeaseManager - para gestão de concessões personalizada.
    * IPartitionProcessor - para alterações ao processamento personalizado numa partição.
* Registo - utiliza a biblioteca [LibLog](https://github.com/damianh/LibLog).
* 100% retrocompatível com a API v1.
* Nova base de código.
* Compatível com as versões 1.21.1 e posteriores do [SDK .NET do SQL](sql-api-sdk-dotnet.md).

### <a name="v1-builds"></a>Compilações v1

### <a name="133"></a><a id="1.3.3"></a>1.3.3
* Adicionados mais registos.
* Corrigida uma fuga em DocumentClient ao chamar a estimativa de trabalho pendente várias vezes.

### <a name="132"></a><a id="1.3.2"></a>1.3.2
* Correções à estimativa de trabalho pendente.

### <a name="131"></a><a id="1.3.1"></a>1.3.1
* Melhorias à estabilidade.
  * Correção ao processamento de tarefas canceladas que pode fazer com que os observadores parem em algumas partições.
* Suporte para pontos de verificação manuais.
* Compatível com as versões 1.21 e posteriores do [SDK de .NET do SQL](sql-api-sdk-dotnet.md).

### <a name="120"></a><a id="1.2.0"></a>1.2.0
* Adiciona suporte para .NET Standard 2.0 O pacote suporta agora os monikers de framework `netstandard2.0` e `net451`.
* Compatível com as versões 1.17.0 e posteriores do [SDK de .NET do SQL](sql-api-sdk-dotnet.md).
* Compatível com as versões 1.5.1 e posteriores do [SDK de .NET Core do SQL](sql-api-sdk-dotnet-core.md).

### <a name="111"></a><a id="1.1.1"></a>1.1.1
* Corrige um problema com o cálculo da estimativa do trabalho restante quando o Feed de Alterações estava vazio ou não havia trabalho pendente.
* Compatível com as versões 1.13.2 e posteriores do [SDK de .NET do SQL](sql-api-sdk-dotnet.md).

### <a name="110"></a><a id="1.1.0"></a>1.1.0
* Adicionado um método para obter uma estimativa do trabalho restante a ser processado no Feed de Alterações.
* Compatível com as versões 1.13.2 e posteriores do [SDK de .NET do SQL](sql-api-sdk-dotnet.md).

### <a name="100"></a><a id="1.0.0"></a>1.0.0
* SDK em Disponibilidade Geral
* Compatível com as versões 1.14.1 e anteriores do [SDK de .NET do SQL](sql-api-sdk-dotnet.md).

## <a name="release--retirement-dates"></a>Datas de lançamento e descontinuação

A Microsoft enviará uma notificação com uma antecedência de pelo menos **12 meses** antes da descontinuação de um SDK, para tornar a transição para uma versão mais recente/suportada mais suave. As funcionalidades, características e otimizações novas só são adicionadas ao SDK atual, pelo que se recomenda que atualize sempre para a última versão dos SDKs o mais cedo possível.

> [!WARNING]
> A partir de 31 de agosto de 2022, o Azure Cosmos DB deixará de fazer correções de erros, adicionar funcionalidades novas e fornecer suporte para as versões 1.x do SDK de .NET do Azure Cosmos DB ou do SDK de .NET Core para a API SQL. Se preferir não atualizar, o serviço Azure Cosmos DB continua a servir os pedidos enviados das versões 1.x do SDK.

<br/>

| Versão | Data da versão: | Data de Extinção |
| --- | --- | --- |
| [2.3.2](#2.3.2) |11 de agosto de 2020 |--- |
| [2.3.1](#2.3.1) |30 de julho de 2020 |--- |
| [2.3.0](#2.3.0) |2 de abril de 2020 |--- |
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

Para saber mais sobre o Azure Cosmos DB, veja a página do serviço [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
