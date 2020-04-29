---
title: Azure Cosmos DB .NET change feed Processador API, notas de lançamento SDK
description: Saiba tudo sobre o Processador de Feed de Mudança API e SDK, incluindo datas de lançamento, datas de reforma e alterações efetuadas entre cada versão do Processador de Feed de Mudança .NET SDK.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 01/30/2019
ms.author: maquaran
ms.openlocfilehash: 5820778d46f5701b82bb289192350a9e13739d37
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80619449"
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>.NET Change Feed Processor SDK: Descarregamento e descarregamento de notas

> [!div class="op_single_selector"]
>
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET Change Feed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java assíncrono](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Fornecedor de Recursos REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Executor a granel - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Executor a granel - Java](sql-api-sdk-bulk-executor-java.md)

|   |   |
|---|---|
|**Download sDK**|[Nuget](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**Documentação da API**|[Alterar documentação de referência da biblioteca de processadores de feed aPI](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**Introdução**|[Começar com o Processador de Feed de Mudança .NET SDK](change-feed.md)|
|**Quadro apoiado atual**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

> [!NOTE]
> Se estiver a utilizar o processador de feed de mudança, consulte a versão mais recente 3.x do [.NET SDK,](change-feed-processor.md)que tem o feed de alteração incorporado no SDK. 

## <a name="release-notes"></a>Notas de versão

### <a name="v2-builds"></a>v2 constrói

### <a name="230"></a><a name="2.3.0"/>2.3.0
* Adicionei um `ChangeFeedProcessorBuilder.WithCheckpointPartitionProcessorFactory` novo método `ICheckpointPartitionProcessorFactory`e interface pública correspondente. Isto permite uma `IPartitionProcessor` implementação da interface para usar mecanismo de controlo incorporado. A nova fábrica é semelhante `IPartitionProcessorFactory`à existente, exceto que o seu `Create` método também leva o `ILeaseCheckpointer` parâmetro.
* Apenas um dos dois `ChangeFeedProcessorBuilder.WithPartitionProcessorFactory` `ChangeFeedProcessorBuilder.WithCheckpointPartitionProcessorFactory`métodos, ou, `ChangeFeedProcessorBuilder` pode ser utilizado para o mesmo caso.

### <a name="228"></a><a name="2.2.8"/>2.2.8
* Melhorias de estabilidade e de diagnosticabilidade:
  * Suporte adicional para detetar a mudança de leitura alimentação demorando muito tempo. Quando demora mais do que o `ChangeFeedProcessorOptions.ChangeFeedTimeout` valor especificado pela propriedade, são tomadas as seguintes etapas:
    * A operação para ler a mudança de alimentos sobre a divisória problemática é abortada.
    * A instância do processador de feed de mudança deixa cair a propriedade do contrato de arrendamento problemático. O arrendamento suspenso será recolhido durante o próximo passo de aquisição de locação que será feito pela mesma ou diferente instância do processador de feed de mudança. Desta forma, a leitura da mudança vai recomeçar.
    * Um problema é reportado ao monitor de saúde. O monitor de saúde predefinido envia todos os problemas reportados para rastrear o registo.
  * Acrescentou uma nova `ChangeFeedProcessorOptions.ChangeFeedTimeout`propriedade pública: . O valor padrão desta propriedade é de 10 minutos.
  * Acrescentou um novo valor `Monitoring.MonitoredOperation.ReadChangeFeed`público enum: . Quando o `HealthMonitoringRecord.Operation` valor é `Monitoring.MonitoredOperation.ReadChangeFeed`definido para , indica que a questão da saúde está relacionada com a mudança de leitura alimentos.

### <a name="227"></a><a name="2.2.7"/>2.2.7
* A melhoria da estratégia de equilíbrio de carga para o cenário ao obter todos os contratos de arrendamento demora mais tempo do que o intervalo de expiração do arrendamento, por exemplo, devido a problemas de rede:
  * Neste cenário, o algoritmo de equilíbrio de carga usado para considerar falsamente os contratos de arrendamento como expirados, causando o roubo de arrendamentos de proprietários ativos. Isto pode desencadear um reequilíbrio desnecessário de muitos contratos de arrendamento.
  * Esta questão é corrigida nesta versão, evitando a repetição do conflito enquanto adquire o arrendamento expirado que o proprietário não mudou e posicionando a aquisição de locação caducada para a próxima iteração de equilíbrio de carga.

### <a name="226"></a><a name="2.2.6"/>2.2.6
* Melhor gestão das exceções do Observador.
* Informação mais rica sobre erros do Observador:
  * Quando um Observador é fechado devido a uma exceção lançada pelo Processo ChangesAsync do Observador, o CloseAsync passará a receber o parâmetro de razão definido para ChangeFeedObserverCloseReason.ObserverError.
  * Vestígios adicionados para identificar erros dentro do código de utilizador num Observador.

### <a name="225"></a><a name="2.2.5"/>2.2.5
* Suporte adicional para manuseamento dividido em coleções que usam a entrada de base de dados partilhada.
  * Esta versão corrige um problema que pode ocorrer durante a divisão em coleções utilizando a entrada de base de dados partilhada quando o resultado dividido em reequilíbrio da divisória com apenas uma gama de chaves de partição infantil criada, em vez de duas. Quando isso acontecer, o Processador change feed pode ficar preso a eliminar o aluguer de uma antiga gama de chaves de partição e não criar novos contratos de arrendamento. A questão está corrigida nesta versão.

### <a name="224"></a><a name="2.2.4"/>2.2.4
* Adicionou nova propriedade ChangeFeedProcessorOptions.StartContinuation para suportar o feed de mudança inicial a partir do token de continuação do pedido. Isto só é usado quando a coleção de arrendamento está vazia ou um arrendamento não tem conjunto De ContinuaçãoToken. Para arrendamentos em coleção de arrendamento que tenham conjunto ContinuationToken, o ContinuationToken é usado e ChangeFeedProcessorOptions.StartContinuation é ignorado.

### <a name="223"></a><a name="2.2.3"/>2.2.3
* Suporte adicional para a utilização de loja personalizada para persistir tokens de continuação por partição.
  * Por exemplo, uma loja de arrendamento personalizada pode ser a coleção de arrendamento Azure Cosmos DB dividida de qualquer forma personalizada.
  * As lojas de arrendamento personalizadas podem usar o novo ponto de extensibility ChangeFeedProcessorBuilder.WithLeaseStoreManager (ILeaseStoreManager) e interface pública ILeaseStoreManager.
  * Refactored a interface iLeaseManager em várias interfaces de funções.
* Pequena alteração de rutura: ponto de extensibility removido ChangeFeedProcessorBuilder.WithLeaseManager (ILeaseManager), use ChangeFeedProcessorBuilder.WithLeaseStoreManager (ILeaseStoreManager) em vez disso.

### <a name="222"></a><a name="2.2.2"/>2.2.2
* Esta versão corrige um problema que ocorre durante o processamento de uma divisão na recolha monitorizada e utilizando uma coleção de arrendamento dividido. Ao processar um contrato de arrendamento para partição dividida, o contrato de arrendamento correspondente a essa divisória não pode ser eliminado. A questão está corrigida nesta versão.

### <a name="221"></a><a name="2.2.1"/>2.2.1
* Cálculo estimador fixo para contas Multi Master e novo formato Sessão Token.

### <a name="220"></a><a name="2.2.0"/>2.2.0
* Apoio adicional para coleções de arrendamento divididos. A chave de partição deve ser definida como /id.
* Pequena alteração de quebra: os métodos da interface IChangeFeedDocumentClient e da classe ChangeFeedDocumentClient foram alterados para incluir os parâmetros RequestOptions e CancelamentoSToken. IChangeFeedDocumentClient é um ponto avançado de extensibility que lhe permite fornecer implementação personalizada do Cliente de Documento para usar com processador de feed de mudança, por exemplo, decorar o DocumentClient e intercetar todas as chamadas para fazer rastreios extra, manipulação de erros, etc. Com esta atualização, o código que implementa o IChangeFeedDocumentClient terá de ser alterado para incluir novos parâmetros na implementação.
* Pequenas melhorias de diagnóstico.

### <a name="210"></a><a name="2.1.0"/>2.1.0
* Adicionou novo API,&lt;Task&lt;IReadOnlyList RemainingPartitionWorkWork&gt; &gt; IRemainingWorkEstimatetor.GetEstimatedRemainingRemainingWorkPerPartitionAsync(). Isto pode ser usado para obter trabalho estimado para cada partição.
* Suporta Microsoft.Azure.DocumentDB SDK 2.0. Requer Microsoft.Azure.DocumentDB 2.0 ou mais tarde.

### <a name="206"></a><a name="2.0.6"/>2.0.6
* Adicionado ChangeFeedEventHost.HostName propriedade pública para compatibilidade com v1.

### <a name="205"></a><a name="2.0.5"/>2.0.5
* Fixou uma condição de corrida que ocorre durante a divisão. A condição de corrida pode levar à aquisição do arrendamento e perdê-la imediatamente durante a divisão e causar contenção. A questão da condição da corrida é corrigida com esta libertação.

### <a name="204"></a><a name="2.0.4"/>2.0.4
* GA SDK

### <a name="203-prerelease"></a><a name="2.0.3-prerelease"/>2.0.3 pré-lançamento
* Foram corrigidos os problemas seguintes:
  * Quando a divisão acontece, pode haver duplicado processamento de documentos modificados antes da divisão.
  * A GetEstimatedRemainingWork API devolveu 0 quando não havia arrendamentos na coleção de arrendamento.

* As seguintes exceções são tornadas públicas. As extensões que implementam o IPartitionProcessor podem lançar estas exceções.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.LeaseLostException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionNotFoundException.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionSplitException. 

### <a name="202-prerelease"></a><a name="2.0.2-prerelease"/>2.0.2 pré-lançamento
* Alterações menores da API:
  * Opções de processador de changeFeed removeu.IsAutoCheckpointEnabled que foi marcado como obsoleto.

### <a name="201-prerelease"></a><a name="2.0.1-prerelease"/>2.0.1 pré-lançamento
* Melhorias da estabilidade:
  * Melhor manuseamento da inicialização da loja de arrendamento. Quando a loja de arrendamento está vazia, apenas uma instância de processador pode rubricar, as outras esperarão.
  * Renovação/libertação de locação mais estável/eficiente. Renovar e libertar um contrato de arrendamento uma partição é independente de renovar outras. Em v1 que foi feito sequencialmente para todas as divisórias.
* Nova API v2:
  * Padrão de construtor para construção flexível do processador: a classe ChangeFeedProcessorBuilder.
    * Pode tomar qualquer combinação de parâmetros.
    * Pode tomar documentClient por exemplo para monitorização e/ou coleção de arrendamento (não disponível em v1).
  * IChangeFeedObserver.ProcessChangesAsync leva agora o CancelamentoToken.
  * IRemainingWorkEstimator - o estimador de trabalho restante pode ser utilizado separadamente do processador.
  * Novos pontos de extebilidade:
    * IPartitionLoadBalanceBalanceStrategy - para equilibrar a carga personalizada de divisórias entre as instâncias do processador.
    * ILease, ILeaseManager - para gestão de arrendamento personalizado.
    * IPartitionProcessor - para alterações de processamento personalizado numa partição.
* Logging - usa biblioteca [LibLog.](https://github.com/damianh/LibLog)
* 100% para trás compatível com v1 API.
* Nova base de código.
* Compatível com as versões [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.21.1 ou superior.

### <a name="v1-builds"></a>v1 constrói

### <a name="133"></a><a name="1.3.3"/>1.3.3
* Adicionei mais madeira.
* Corrigiu uma fuga de informação do Cliente documental ao ligar várias vezes à estimativa de trabalho pendente.

### <a name="132"></a><a name="1.3.2"/>1.3.2
* Correções na estimativa de trabalho pendente.

### <a name="131"></a><a name="1.3.1"/>1.3.1
* Melhorias de estabilidade.
  * Correção para lidar com a questão das tarefas canceladas que podem levar a que os observadores parados em algumas divisórias.
* Suporte para controlo manual.
* Compatível com as versões [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.21 ou superior.

### <a name="120"></a><a name="1.2.0"/>1.2.0
* Adiciona suporte para .NET Standard 2.0. O pacote agora `netstandard2.0` `net451` apoia e as alcunhas-quadro.
* Compatível com as versões [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.17.0 ou superior.
* Compatível com as versões [SQL .NET Core SDK](sql-api-sdk-dotnet-core.md) 1.5.1 ou superior.

### <a name="111"></a><a name="1.1.1"/>1.1.1
* Corrige um problema com o cálculo da estimativa do trabalho remanescente quando o Change Feed estava vazio ou não estava pendente nenhum trabalho.
* Compatível com as versões [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.13.2 ou superior.

### <a name="110"></a><a name="1.1.0"/>1.1.0
* Adicione um método para obter uma estimativa do trabalho restante a ser processado no Change Feed.
* Compatível com as versões [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.13.2 ou superior.

### <a name="100"></a><a name="1.0.0"/>1.0.0
* GA SDK
* Compatível com as versões [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.14.1 e abaixo.

## <a name="release--retirement-dates"></a>Datas de lançamento & de aposentadoria

A Microsoft irá notificar com pelo menos **12 meses** de antecedência a retirada de um SDK de forma a suavizar a transição para uma versão mais recente/suportada.

Novas funcionalidades e funcionalidades e otimizações só são adicionadas ao SDK atual, como tal é recomendado que você sempre atual ize para a versão Mais recente SDK o mais cedo possível. 

Qualquer pedido à Cosmos DB utilizando um SDK reformado será rejeitado pelo serviço.

<br/>

| Versão | Data de Lançamento | Data de Aposentadoria |
| --- | --- | --- |
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

Para saber mais sobre cosmos DB, consulte a página de serviço [do Microsoft Azure Cosmos DB.](https://azure.microsoft.com/services/cosmos-db/)
