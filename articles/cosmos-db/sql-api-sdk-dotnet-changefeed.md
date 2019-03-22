---
title: 'Do Azure Cosmos DB: .NET alterar Feed processador API, SDK e recursos'
description: Saiba tudo sobre a API de processador do Feed de alterações e o SDK, incluindo as datas de lançamento, datas de extinção e as alterações feitas entre cada versão do SDK do processador de .NET de Feed de alterações.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 01/30/2019
ms.author: maquaran
ms.openlocfilehash: a878ab1937b06f06a27b18f793fc1bfa190969ed
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58090343"
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>SDK do processador do Feed de alterações de .NET: Transferir e notas de versão
> [!div class="op_single_selector"]
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
> * [BulkExecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor - Java](sql-api-sdk-bulk-executor-java.md)

|   |   |
|---|---|
|**Transferência de SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**Documentação da API**|[Alterar a documentação de referência da API de biblioteca de processador do Feed](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**Introdução**|[Introdução ao SDK de .NET de processador de Feed de alterações](change-feed.md)|
|**Estrutura de suporte atual**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

## <a name="release-notes"></a>Notas de versão

### <a name="v2-builds"></a>compilações de v2

### <a name="a-name226226"></a><a name="2.2.6"/>2.2.6
* Processamento melhorado de exceções de observador.
* Obter informações mais detalhadas sobre os erros de observador:
  * Quando um observador é fechado devido a uma exceção gerada pelo ProcessChangesAsync do observador, o CloseAsync agora receberá o parâmetro de motivo definido como ChangeFeedObserverCloseReason.ObserverError.
  * Foi adicionados rastreios para identificar erros no código do usuário num observador.

### <a name="a-name225225"></a><a name="2.2.5"/>2.2.5
* Foi adicionado suporte para a manipulação de divisão em coleções que utilizam o débito de base de dados partilhada.
  * Esta versão corrige um problema que possam ocorrer durante a divisão em coleções com débito de base de dados partilhada, quando dividir o resultado em partição balanceamento novamente com o intervalo de chaves de partição de apenas um filho criado, em vez de dois. Quando isto acontecer, processador do Feed de alterações pode ficar bloqueado a eliminar a concessão para o intervalo de chaves de partição antigo e não a criar novas concessões. O problema é resolvido nesta versão.

### <a name="a-name224224"></a><a name="2.2.4"/>2.2.4
* Feed adicionada nova propriedade ChangeFeedProcessorOptions.StartContinuation para suportar a alteração de partida do token de continuação do pedido. Esta opção só é utilizada quando a coleção de concessão está vazia ou uma concessão não tem ContinuationToken definido. Para concessões na coleção de concessão com ContinuationToken definido, é utilizado o ContinuationToken e ChangeFeedProcessorOptions.StartContinuation é ignorada.

### <a name="a-name223223"></a><a name="2.2.3"/>2.2.3
* Foi adicionado suporte para utilizar o arquivo personalizado para manter os tokens de continuação por partição.
  * Por exemplo, um arquivo de concessão personalizado pode ser a coleção de concessão do Azure Cosmos DB particionada de forma alguma personalizada.
  * Arquivos de concessão personalizadas podem utilizar o novo ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager) de ponto de extensibilidade e a interface pública de ILeaseStoreManager.
  * Refatorada a interface de ILeaseManager em várias interfaces de função.
* Alteração significativa secundárias: extensibilidade removida ponto ChangeFeedProcessorBuilder.WithLeaseManager(ILeaseManager), utilize ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager) em vez disso.

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2
* Esta versão corrige um problema que ocorre durante o processamento de uma divisão na coleção monitorizada e utilizar uma coleção de concessão particionada. Ao processar uma concessão para a partição de divisão, a concessão correspondente a essa partição não pode ser eliminada. O problema é resolvido nesta versão.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* Cálculo da Calculadora fixo para contas de principal múltiplo e o novo formato de Token de sessão.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Foi adicionado suporte para coleções particionadas concessão. A chave de partição tem de ser definida como/ID.
* Alteração significativa secundárias: os métodos da interface do IChangeFeedDocumentClient e a classe de ChangeFeedDocumentClient foram alterados para incluir parâmetros de RequestOptions e CancellationToken. IChangeFeedDocumentClient é um ponto de extensibilidade avançada que permite-lhe fornecer uma implementação personalizada do cliente do documento para utilizar com o processador do Feed de alterações, por exemplo, para decorar o DocumentClient e interceptar todas as chamadas para ele fazer rastreamento extra, tratamento de erros , etc. Com esta atualização, o código que implementam IChangeFeedDocumentClient tem de ser alterada para incluir novos parâmetros na implementação.
* Melhorias de diagnóstico pequenas.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Adicionada a nova API, tarefa&lt;IReadOnlyList&lt;RemainingPartitionWork&gt; &gt; IRemainingWorkEstimator.GetEstimatedRemainingWorkPerPartitionAsync(). Isto pode ser utilizado para fazer o trabalho estimado para cada partição.
* Suporta Microsoft.Azure.DocumentDB SDK 2.0. Requer Microsoft.Azure.DocumentDB 2.0 ou posterior.

### <a name="a-name206206"></a><a name="2.0.6"/>2.0.6
* Foi adicionada ChangeFeedEventHost.HostName propriedade pública para compatibilidade com v1.

### <a name="a-name205205"></a><a name="2.0.5"/>2.0.5
* Corrigido uma condição de corrida que ocorre durante a divisão de partição. A condição de corrida pode levar a adquirir concessão e perdê-lo durante a divisão de partição imediatamente e causar contenção. O problema de condição de corrida é resolvido com esta versão.

### <a name="a-name204204"></a><a name="2.0.4"/>2.0.4
* SDK DE DISPONIBILIDADE GERAL

### <a name="a-name203-prerelease203-prerelease"></a><a name="2.0.3-prerelease"/>2.0.3-prerelease
* Foram corrigidos os problemas seguintes:
  * Quando ocorre a divisão de partição, pode haver duplicado de processamento de documentos modificados antes da divisão.
  * A API de GetEstimatedRemainingWork devolveu 0 quando não existem concessões estavam presentes na coleção de concessão.

* As seguintes exceções são públicos. As extensões que implementam IPartitionProcessor podem emitir essas exceções.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.LeaseLostException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionNotFoundException.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionSplitException. 

### <a name="a-name202-prerelease202-prerelease"></a><a name="2.0.2-prerelease"/>2.0.2-prerelease
* Pequenas alterações de API:
  * Removido ChangeFeedProcessorOptions.IsAutoCheckpointEnabled que foi marcado como obsoleto.

### <a name="a-name201-prerelease201-prerelease"></a><a name="2.0.1-prerelease"/>2.0.1-prerelease
* Melhorias de estabilidade:
  * Um manuseio de inicialização de arquivo de concessão. Quando o arquivo de concessão estiver vazio, apenas uma instância de processador pode inicializá-lo, irão esperar a outras pessoas.
  * Renovação de concessão stable/eficiente mais/release. Renovação e lançar uma partição de uma concessão são independente da renovação de outras pessoas. No v1, isso foi feito em seqüência para todas as partições.
* Nova API v2:
  * Padrão de construtor para construção flexível do processador: a classe ChangeFeedProcessorBuilder.
    * Pode demorar qualquer combinação de parâmetros.
    * Pode demorar a instância do DocumentClient para a recolha de monitorização e/ou concessão (não disponível no v1).
  * IChangeFeedObserver.ProcessChangesAsync now takes CancellationToken.
  * IRemainingWorkEstimator - o avaliador de trabalho restantes pode ser utilizado em separado do processador.
  * Novos pontos de extensibilidade:
    * IPartitionLoadBalancingStrategy - para personalizados-balanceamento de carga de partições entre instâncias do processador.
    * ILease, ILeaseManager - para a gestão da concessão personalizado.
    * IPartitionProcessor - para que as alterações do processamento personalizado numa partição.
* Registo - utiliza [LibLog](https://github.com/damianh/LibLog) biblioteca.
* 100% compatível com versões anteriores com a API de v1.
* Nova base de código.
* Compatível com [SDK de .NET de SQL](sql-api-sdk-dotnet.md) versões 1.21.1 e acima.

### <a name="v1-builds"></a>compilações de v1

### <a name="a-name133133"></a><a name="1.3.3"/>1.3.3
* Adicionar mais registo.
* Foi corrigido um vazamento de DocumentClient ao chamar a estimativa do trabalho pendente várias vezes.

### <a name="a-name132132"></a><a name="1.3.2"/>1.3.2
* Correções a estimativa do trabalho pendente.

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1
* Melhorias de estabilidade.
  * Correção para a manipulação de problema de tarefas foi cancelada, que pode levar a parada observadores em algumas partições.
* Suporte para pontos de verificação manual.
* Compatível com [SDK de .NET de SQL](sql-api-sdk-dotnet.md) versões 1,21 e acima.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Adiciona suporte para .NET Standard 2.0. O pacote suporta agora `netstandard2.0` e `net451` monikers do framework.
* Compatível com [SDK de .NET de SQL](sql-api-sdk-dotnet.md) versões 1.17.0 e acima.
* Compatível com [SQL .NET Core SDK](sql-api-sdk-dotnet-core.md) versões 1.5.1 e acima.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1
* Corrige um problema com o cálculo de estimativa do trabalho restante, quando o Feed de alteração estava vazio ou nenhum trabalho estava pendente.
* Compatível com [SDK de .NET de SQL](sql-api-sdk-dotnet.md) versões 1.13.2 e acima.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Adicionar um método para obter uma estimativa do trabalho restante para serem processados no Feed de alterações.
* Compatível com [SDK de .NET de SQL](sql-api-sdk-dotnet.md) versões 1.13.2 e acima.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* SDK DE DISPONIBILIDADE GERAL
* Compatível com [SDK de .NET de SQL](sql-api-sdk-dotnet.md) versões 1.14.1 e abaixo.


## <a name="release--retirement-dates"></a>Datas de lançamento & retirada
A Microsoft irá fornecer, pelo menos, notificação **12 meses** antecedência extinguir um SDK para facilitar a transição para uma versão mais recente/suportadas.

Novos recursos e funcionalidade e otimizações, apenas são adicionadas ao SDK atual, como tal, é recomendável que sempre atualiza para a versão mais recente SDK mais cedo possível. 

Qualquer pedido ao Cosmos DB com um SDK extinto será rejeitado pelo serviço.

<br/>

| Versão | Data de lançamento | Data de retirada |
| --- | --- | --- |
| [2.2.6](#2.2.6) |29 de Janeiro de 2019 |--- |
| [2.2.5](#2.2.5) |13 de Dezembro de 2018 |--- |
| [2.2.4](#2.2.4) |29 de Novembro de 2018 |--- |
| [2.2.3](#2.2.3) |19 de Novembro de 2018 |--- |
| [2.2.2](#2.2.2) |31 de Outubro de 2018 |--- |
| [2.2.1](#2.2.1) |24 de Outubro de 2018 |--- |
| [1.3.3](#1.3.3) |08 de Maio de 2018 |--- |
| [1.3.2](#1.3.2) |18 de abril de 2018 |--- |
| [1.3.1](#1.3.1) |13 de março de 2018 |--- |
| [1.2.0](#1.2.0) |31 de outubro de 2017 |--- |
| [1.1.1](#1.1.1) |29 de Agosto de 2017 |--- |
| [1.1.0](#1.1.0) |13 de Agosto de 2017 |--- |
| [1.0.0](#1.0.0) |07 de Julho de 2017 |--- |


## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Consulte também
Para saber mais sobre o Cosmos DB, veja [do Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) página do serviço. 

