---
title: Azure Cosmos DB .NET altera processador de alimentação API, notas de lançamento SDK
description: Saiba tudo sobre o Processador de Change Feed API e SDK, incluindo datas de lançamento, datas de reforma e alterações efetuadas entre cada versão do Processador de Feed de Alteração .NET SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/05/2020
ms.author: anfeldma
ms.openlocfilehash: f38b2715115efadef4e09a95e9392b1dfd4c68b0
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2020
ms.locfileid: "88135747"
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>.NET Change Feed Processor SDK: Baixar e lançar notas

> [!div class="op_single_selector"]
>
> * [SDK .NET v3](sql-api-sdk-dotnet-standard.md)
> * [SDK .NET v2](sql-api-sdk-dotnet.md)
> * [SDK v2 de .NET Core](sql-api-sdk-dotnet-core.md)
> * [.NET Change Feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [SDK v4 de Java](sql-api-sdk-java-v4.md)
> * [SDK v2 Java assíncrono](sql-api-sdk-async-java.md)
> * [SDK v2 Java síncrono](sql-api-sdk-java.md)
> * [Spring Data](sql-api-sdk-java-spring.md)
> * [Conector de faíscas](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [DESCANSAR] (/repouso/api
> * [Provedor de Recursos REST] (/repouso/api
> * [SQL](sql-api-query-reference.md)
> * [Executor a granel - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Executor a granel - Java](sql-api-sdk-bulk-executor-java.md)

|   |   |
|---|---|
|**Download SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**Documentação da API**|[Alterar documentação de referência da biblioteca de processadores de alimentação API](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**Introdução**|[Começar com o processador Change Feed .NET SDK](change-feed.md)|
|**Quadro atual suportado**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

> [!NOTE]
> Se estiver a utilizar o processador change feed, consulte a versão mais recente 3.x do [.NET SDK](change-feed-processor.md), que tem o feed de mudança incorporado no SDK. 

## <a name="release-notes"></a>Notas de versão

### <a name="v2-builds"></a>v2 constrói

### <a name="232"></a><a name="2.3.2"/>2.3.2
* Compatibilidade de loja de arrendamento adicionada com [V3 SDK](sql-api-sdk-dotnet-standard.md) que permite caminhos de migração quente. Uma aplicação pode migrar para V3 SDK e migrar de volta para a biblioteca do processador Change Feed sem perder qualquer estado.

### <a name="231"></a><a name="2.3.1"/>2.3.1
* Corrigiu um caso quando `FeedProcessing.ChangeFeedObserverCloseReason.Unknown` foi enviada uma razão próxima para se `FeedProcessing.IChangeFeedObserver.CloseAsync` a partição não puder ser encontrada ou se a réplica do alvo não estiver atualizada com a sessão de leitura. Nestes `FeedProcessing.ChangeFeedObserverCloseReason.ResourceGone` casos, `FeedProcessing.ChangeFeedObserverCloseReason.ReadSessionNotAvailable` são agora utilizadas razões próximas.
* Acrescentou uma nova razão próxima `FeedProcessing.ChangeFeedObserverCloseReason.ReadSessionNotAvailable` que é enviada para fechar o observador de feed de alteração quando a réplica do alvo não está atualizada com a sessão de leitura.

### <a name="230"></a><a name="2.3.0"/>2.3.0
* Adicionou um novo método `ChangeFeedProcessorBuilder.WithCheckpointPartitionProcessorFactory` e a correspondente interface `ICheckpointPartitionProcessorFactory` pública. Isto permite que uma implementação da interface utilize o `IPartitionProcessor` mecanismo de verificação incorporado. A nova fábrica é semelhante à existente, exceto que o `IPartitionProcessorFactory` seu método também toma o `Create` `ILeaseCheckpointer` parâmetro.
* Apenas um dos dois métodos, `ChangeFeedProcessorBuilder.WithPartitionProcessorFactory` `ChangeFeedProcessorBuilder.WithCheckpointPartitionProcessorFactory` ou, pode ser usado para o mesmo `ChangeFeedProcessorBuilder` caso.

### <a name="228"></a><a name="2.2.8"></a>2.2.8
* Melhorias de estabilidade e diagnóstico:
  * Suporte adicionado para detetar a alteração de leitura do feed que demora muito tempo. Quando demora mais do que o valor especificado pela `ChangeFeedProcessorOptions.ChangeFeedTimeout` propriedade, são tomadas as seguintes medidas:
    * A operação de leitura da alteração da partilha problemática é abortada.
    * A instância do processador de modificação deixa cair a propriedade do arrendamento problemático. O arrendamento abandonado será recolhido durante o próximo passo de aquisição de arrendamento que será feito pela mesma instância ou diferente do processador de feed de mudança. Desta forma, a leitura da mudança de alimentação recomeçará.
    * Um problema é reportado ao monitor de saúde. O monitor de saúde predefinido envia todos os problemas reportados para rastrear o registo.
  * Acrescentou uma nova propriedade pública: `ChangeFeedProcessorOptions.ChangeFeedTimeout` . O valor padrão desta propriedade é de 10 minutos.
  * Acrescentou um novo valor público enum: `Monitoring.MonitoredOperation.ReadChangeFeed` . Quando o valor de `HealthMonitoringRecord.Operation` , indica que a `Monitoring.MonitoredOperation.ReadChangeFeed` questão da saúde está relacionada com a leitura do feed de mudança.

### <a name="227"></a><a name="2.2.7"></a>2.2.7
* Uma estratégia melhorada de equilíbrio de carga para cenário quando se obter todos os contratos de arrendamento demora mais tempo do que o intervalo de validade do arrendamento, por exemplo, devido a problemas de rede:
  * Neste cenário, o algoritmo de equilíbrio de carga usado para considerar falsamente os contratos de arrendamento como expirados, causando o roubo de arrendamentos de proprietários ativos. Isto pode desencadear um reequilíbrio desnecessário de muitos contratos de arrendamento.
  * Esta questão é corrigida nesta versão, evitando a repetição do conflito enquanto adquire contrato de arrendamento vencido que o proprietário não mudou e adia a aquisição de arrendamento vencido para a próxima iteração de equilíbrio de carga.

### <a name="226"></a><a name="2.2.6"></a>2.2.6
* Melhor tratamento das exceções do Observador.
* Informação mais rica sobre erros do Observador:
  * Quando um Observador estiver fechado devido a uma exceção lançada pelo ProcessChangesAsync do Observador, o CloseAsync receberá agora o parâmetro de razão definido para ChangeFeedObserverCloseReason.ObserverError.
  * Foram acrescentados vestígios para identificar erros dentro do código do utilizador num Observador.

### <a name="225"></a><a name="2.2.5"></a>2.2.5
* Suporte adicional para manuseamento dividido em coleções que usam o rendimento da base de dados partilhada.
  * Esta versão corrige um problema que pode ocorrer durante a divisão em coleções utilizando a produção de base de dados partilhada quando o resultado dividido em reequilíbrio de partição com apenas uma gama de chaves de partição infantil criada, em vez de duas. Quando isso acontece, o Processador Change Feed pode ficar preso eliminando o arrendamento para a gama de chaves de partição antiga e não criar novos arrendamentos. A questão está corrigida nesta versão.

### <a name="224"></a><a name="2.2.4"></a>2.2.4
* Adicionou novos imóveis ChangeFeedProcessorOptions.StartContinuation para suportar o feed de alteração inicial a partir de sinal de continuação do pedido. Isto só é usado quando a coleção de arrendamento está vazia ou um arrendamento não tem continuationToken definido. Para arrendamentos em coleção de arrendamento que tenham conjunto ContinuationToken, o ContinuationToken é usado e ChangeFeedProcessorOptions.StartContinuation é ignorado.

### <a name="223"></a><a name="2.2.3"></a>2.2.3
* Suporte adicional para usar a loja personalizada para persistir tokens de continuação por partição.
  * Por exemplo, uma loja de arrendamento personalizada pode ser Azure Cosmos DB coleção de aluguel dividido de qualquer forma personalizada.
  * As lojas de arrendamento personalizadas podem utilizar novos pontos de extensibilidade ChangeFeedProcessorBuilder.WithLeaseStoreManager (ILeaseStoreManager) e interface pública ILeaseStoreManager.
  * Refactorou a interface ILeaseManager em interfaces de papel múltiplas.
* Pequena alteração de rutura: ponto de extensibilidade removido ChangeFeedProcessorBuilder.WithLeaseManager (ILeaseManager), use ChangeFeedProcessorBuilder.WithLeaseStoreManager (ILeaseStoreManager) em vez disso.

### <a name="222"></a><a name="2.2.2"></a>2.2.2
* Este lançamento corrige um problema que ocorre durante o processamento de uma divisão na recolha monitorizada e usando uma coleção de locação dividida. Ao processar um arrendamento para partição dividida, o arrendamento correspondente a essa divisória não pode ser eliminado. A questão está corrigida nesta versão.

### <a name="221"></a><a name="2.2.1"></a>2.2.1
* Cálculo do Estimador Fixo para contas Multi Master e novo formato Session Token.

### <a name="220"></a><a name="2.2.0"></a>2.2.0
* Apoio adicional para coleções de arrendamento dividido. A chave de partição deve ser definida como /id.
* Pequena alteração de rutura: os métodos da interface IChangeFeedDocumentClient e da classe ChangeFeedDocumentClient foram alterados para incluir os parâmetros RequestOptions e CancelamentoToken. IChangeFeedDocumentClient é um ponto de extensibilidade avançado que lhe permite fornecer a implementação personalizada do Cliente documental para usar com o Processador Change Feed, por exemplo, decorar o DocumentClient e intercetar todas as chamadas para ele para fazer rastreio extra, manipulação de erros, etc. Com esta atualização, o código que implementa o IChangeFeedDocumentClient terá de ser alterado para incluir novos parâmetros na implementação.
* Pequenas melhorias de diagnóstico.

### <a name="210"></a><a name="2.1.0"></a>2.1.0
* Adicionado novo API, Task &lt; IReadOnlyList &lt; RemainingPartitionWork &gt; &gt; IRemainingWorkEstimator.GetEstimatedRemainingWorkPerPartitionAsync(). Isto pode ser usado para obter trabalho estimado para cada divisória.
* Suporta Microsoft.Azure.DocumentDB SDK 2.0. Requer Microsoft.Azure.DocumentDB 2.0 ou mais tarde.

### <a name="206"></a><a name="2.0.6"></a>2.0.6
* Adicionado ChangeFeedEventHost.HostName propriedade pública para compatibilidade com v1.

### <a name="205"></a><a name="2.0.5"></a>2.0.5
* Corrigiu uma condição de corrida que ocorre durante a divisão de divisórias. A condição da corrida pode levar à aquisição do arrendamento e imediatamente perdê-lo durante a divisão de divisórias e causar contenção. O problema da condição de corrida é corrigido com esta libertação.

### <a name="204"></a><a name="2.0.4"></a>2.0.4
* GA SDK

### <a name="203-prerelease"></a><a name="2.0.3-prerelease"></a>2.0.3 pré-relançse
* Foram corrigidos os problemas seguintes:
  * Quando a divisão acontece, pode haver processamento duplicado de documentos modificados antes da divisão.
  * A API GetEstimatedRemainingWork devolveu 0 quando não havia contratos de arrendamento na coleção de arrendamento.

* As seguintes exceções são tornadas públicas. Extensões que implementam o IPartitionProcessor podem lançar estas exceções.
  * Microsoft.Azure.Documents. ChangeFeedProcessor.Exceptions.LeaseLostException. 
  * Microsoft.Azure.Documents. ChangeFeedProcessor.Exceptions.PartitionException. 
  * Microsoft.Azure.Documents. ChangeFeedProcessor.Exceptions.PartitionNotFoundException.
  * Microsoft.Azure.Documents. ChangeFeedProcessor.Exceptions.PartitionSplitException. 

### <a name="202-prerelease"></a><a name="2.0.2-prerelease"></a>2.0.2 pré-relançse
* Alterações menores da API:
  * Removeu changeFeedProcessorOptions.IsAutoCheckpointEnabled que foi marcado como obsoleto.

### <a name="201-prerelease"></a><a name="2.0.1-prerelease"></a>2.0.1 pré-relançse
* Melhorias de estabilidade:
  * Melhor manuseamento da inicialização da loja de arrendamento. Quando a loja de arrendamento está vazia, apenas uma instância do processador pode inicializá-la, os outros vão esperar.
  * Renovação/libertação de arrendamento mais estável/eficiente. Renovar e libertar um arrendamento de uma divisória é independente de renovar outras. Em v1 que foi feito sequencialmente para todas as divisórias.
* Nova API v2:
  * Padrão de construtor para construção flexível do processador: a classe ChangeFeedProcessorBuilder.
    * Pode tomar qualquer combinação de parâmetros.
    * Pode tomar exemplo de DocumentoClient para monitorização e/ou recolha de locação (não disponível em v1).
  * IChangeFeedObserver.ProcessChangesAsync toma agora o CancelamentoToken.
  * IRemainingWorkEstimator - o restante estimador de trabalho pode ser utilizado separadamente do processador.
  * Novos pontos de extensibilidade:
    * IPartitionLoadBalancingStrategy - para o equilíbrio personalizado de partículas entre as instâncias do processador.
    * ILease, ILeaseManager - para gestão de arrendamento personalizado.
    * IPartitionProcessor - para alterações de processamento personalizado numa partição.
* Logging - utiliza a biblioteca [LibLog.](https://github.com/damianh/LibLog)
* 100% compatível com V1 API.
* Nova base de códigos.
* Compatível com as versões [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.21.1 e superior.

### <a name="v1-builds"></a>v1 constrói

### <a name="133"></a><a name="1.3.3"></a>1.3.3
* Adicione mais registos.
* Corrigiu uma fuga de documentos ao ligar várias vezes para a estimativa de trabalho pendente.

### <a name="132"></a><a name="1.3.2"></a>1.3.2
* Correções na estimativa de trabalho pendente.

### <a name="131"></a><a name="1.3.1"></a>1.3.1
* Melhorias de estabilidade.
  * Correção para lidar com o problema de tarefas canceladas que pode levar a observadores parados em algumas divisórias.
* Suporte para verificação manual.
* Compatível com [as versões SQL .NET SDK](sql-api-sdk-dotnet.md) 1.21 e superiores.

### <a name="120"></a><a name="1.2.0"></a>1.2.0
* Adiciona suporte para .NET Standard 2.0. O pacote agora apoia e os `netstandard2.0` `net451` nomes-quadro.
* Compatível com as versões [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.17.0 ou superior.
* Compatível com as versões [SQL .NET Core SDK](sql-api-sdk-dotnet-core.md) 1.5.1 e superior.

### <a name="111"></a><a name="1.1.1"></a>1.1.1
* Corrige um problema com o cálculo da estimativa do trabalho remanescente quando o Feed de Alteração estava vazio ou nenhum trabalho estava pendente.
* Compatível com as versões [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.13.2 ou superior.

### <a name="110"></a><a name="1.1.0"></a>1.1.0
* Adicionei um método para obter uma estimativa do trabalho restante a ser processado no Feed de Alteração.
* Compatível com as versões [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.13.2 ou superior.

### <a name="100"></a><a name="1.0.0"></a>1.0.0
* GA SDK
* Compatível com as versões [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.14.1 e abaixo.

## <a name="release--retirement-dates"></a>Liberação & Datas de Reforma

A Microsoft fornecerá a notificação com pelo menos **12 meses** de antecedência para retirar um SDK de forma a suavizar a transição para uma versão mais recente/suportada. Novas funcionalidades e funcionalidades e otimizações são adicionadas apenas ao SDK atual, como tal é recomendado que você sempre atualize para a versão SDK mais recente o mais cedo possível.

> [!WARNING]
> Depois de 31 de agosto de 2022, a Azure Cosmos DB deixará de fazer correções de bugs, adicionar novas funcionalidades e fornecer suporte às versões 1.x do Azure Cosmos DB .NET ou .NET Core SDK para a SQL API. Se preferir não fazer upgrade, os pedidos enviados da versão 1.x do SDK continuarão a ser servidos pelo serviço DB Azure Cosmos.

<br/>

| Versão | Data de Lançamento | Data de Extinção |
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

## <a name="see-also"></a>Ver também

Para saber mais sobre cosmos DB, consulte a página de serviço [do Microsoft Azure Cosmos DB.](https://azure.microsoft.com/services/cosmos-db/)
