---
title: Resolução de problemas Azure Cosmos DB HTTP 408 ou solicitar problemas de timeout com o .NET SDK
description: Saiba como diagnosticar e corrigir .NET SDK solicitar exceções de tempo limite.
author: j82w
ms.service: cosmos-db
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: 735c098fcf6fed76019850a1cb58d9eb6c485b7a
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101022"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-net-sdk-request-timeout-exceptions"></a>Diagnosticar e resolver problemas Azure Cosmos DB .NET SDK solicitar exceções de tempo limite
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

O erro HTTP 408 ocorre se o SDK não conseguir completar o pedido antes do prazo de tempo.

## <a name="customize-the-timeout-on-the-azure-cosmos-db-net-sdk"></a>Personalize o tempo limite no Azure Cosmos DB .NET SDK

O SDK tem duas alternativas distintas para controlar os intervalos de tempo, cada um com um âmbito diferente.

### <a name="requesttimeout"></a>RequestTimeout

A `CosmosClientOptions.RequestTimeout` configuração (ou `ConnectionPolicy.RequestTimeout` para SDK v2) permite-lhe definir um tempo limite que afeta cada pedido de rede individual. Uma operação iniciada por um utilizador pode abranger vários pedidos de rede (por exemplo, pode haver estrangulamento). Esta configuração aplicar-se-ia a cada pedido de rede na repetição. Este intervalo não é um tempo de pedido de operação de ponta a ponta.

### <a name="cancellationtoken"></a>CancelamentoToken

Todas as operações de async no SDK têm um parâmetro de CancelamentoToken opcional. Este parâmetro [CancelamentoToken](/dotnet/standard/threading/how-to-listen-for-cancellation-requests-by-polling) é utilizado durante toda a operação, em todos os pedidos de rede. Entre os pedidos de rede, o token de cancelamento pode ser verificado e uma operação cancelada se o token relacionado for expirado. O token de cancelamento deve ser utilizado para definir um intervalo esperado aproximado no âmbito da operação.

> [!NOTE]
> O `CancellationToken` parâmetro é um mecanismo onde a biblioteca verificará o cancelamento quando não [causará um estado inválido](https://devblogs.microsoft.com/premier-developer/recommended-patterns-for-cancellationtoken/). A operação pode não cancelar exatamente quando a hora definida no cancelamento é para cima. Em vez disso, depois de acabar o tempo, cancela quando é seguro fazê-lo.

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas
A lista que se segue contém causas e soluções conhecidas para solicitar exceções no prazo de tempo.

### <a name="high-cpu-utilization"></a>Alta utilização do CPU
Alta utilização do CPU é o caso mais comum. Para uma latência ótima, o uso do CPU deve ser de aproximadamente 40%. Utilize 10 segundos como intervalo para monitorizar a utilização máxima (não média) do CPU. Os picos de CPU são mais comuns com consultas de divisórias cruzadas onde pode fazer múltiplas ligações para uma única consulta.

#### <a name="solution"></a>Solução:
A aplicação do cliente que utiliza o SDK deve ser dimensionada ou para fora.

### <a name="socket-or-port-availability-might-be-low"></a>A disponibilidade da tomada ou da porta pode ser baixa
Ao correr em Azure, os clientes que usam o .NET SDK podem atingir a exaustão da porta Azure SNAT (PAT).

#### <a name="solution-1"></a>Solução 1:
Se estiver a correr em VMs Azure, siga o guia de [exaustão da porta SNAT](troubleshoot-dot-net-sdk.md#snat).

#### <a name="solution-2"></a>Solução 2:
Se estiver a executar o Azure App Service, siga o [guia de resolução de erros de ligação](../app-service/troubleshoot-intermittent-outbound-connection-errors.md#cause) e [utilize diagnósticos do Serviço de Aplicações](https://azure.github.io/AppService/2018/03/01/Deep-Dive-into-TCP-Connections-in-App-Service-Diagnostics.html).

#### <a name="solution-3"></a>Solução 3:
Se estiver a executar funções Azure, verifique se está a seguir a recomendação da [Azure Functions](../azure-functions/manage-connections.md#static-clients) de manter clientes singleton ou estáticos para todos os serviços envolvidos (incluindo Azure Cosmos DB). Verifique os [limites](../azure-functions/functions-scale.md#service-limits) de serviço com base no tipo e tamanho do alojamento da App de Função.

#### <a name="solution-4"></a>Solução 4:
Se utilizar um representante HTTP, certifique-se de que consegue suportar o número de ligações configuradas no SDK `ConnectionPolicy` . Caso contrário, enfrentará problemas de ligação.

### <a name="create-multiple-client-instances"></a>Criar múltiplas instâncias de clientes
A criação de múltiplas instâncias de clientes pode levar a problemas de contenção de ligação e de tempo limite.

#### <a name="solution"></a>Solução:
Siga as [dicas de desempenho](performance-tips-dotnet-sdk-v3-sql.md#sdk-usage)e use um único exemplo cosmosclient em todo um processo.

### <a name="hot-partition-key"></a>Chave de partição quente
AZure Cosmos DB distribui a produção global a provisionada uniformemente através de divisórias físicas. Quando há uma partição quente, uma ou mais teclas de partição lógica numa partição física estão a consumir todas as Unidades de Pedido de Partição Física por segundo (RU/s). Ao mesmo tempo, o RU/S em outras divisórias físicas não está a ser utilizado. Como sintoma, o total de RU/s consumidos será inferior ao TOTAL previsto ru/s na base de dados ou contentor, mas ainda verá estrangulamento (429s) nos pedidos contra a chave de partição lógica quente. Utilize a [métrica de consumo de RU normalizada](monitor-normalized-request-units.md) para ver se a carga de trabalho encontra uma divisória quente. 

#### <a name="solution"></a>Solução:
Escolha uma boa chave de partição que distribui uniformemente o volume e armazenamento do pedido. Saiba como mudar a [sua tecla de partição.](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/)

### <a name="high-degree-of-concurrency"></a>Elevado grau de concordância
A aplicação está a fazer um alto nível de concordância, o que pode levar a uma contenda no canal.

#### <a name="solution"></a>Solução:
A aplicação do cliente que utiliza o SDK deve ser dimensionada ou para fora.

### <a name="large-requests-or-responses"></a>Grandes pedidos ou respostas
Grandes pedidos ou respostas podem levar a bloqueios de linha no canal e exacerbar a contenção, mesmo com um grau de concordância relativamente baixo.

#### <a name="solution"></a>Solução:
A aplicação do cliente que utiliza o SDK deve ser dimensionada ou para fora.

### <a name="failure-rate-is-within-the-azure-cosmos-db-sla"></a>A taxa de falha está dentro do Azure Cosmos DB SLA
A aplicação deve ser capaz de lidar com falhas transitórias e tentar novamente quando necessário. Quaisquer 408 exceções não são novamente julgadas porque na criação de caminhos é impossível saber se o serviço criou o item ou não. Enviar o mesmo item novamente para criar irá causar uma exceção de conflito. A lógica de negócio das aplicações dos utilizadores pode ter uma lógica personalizada para lidar com conflitos, o que quebraria a ambiguidade de um item existente versus conflito de uma criação de novamente.

### <a name="failure-rate-violates-the-azure-cosmos-db-sla"></a>Taxa de falha viola o Azure Cosmos DB SLA
Contacte [o Suporte Azure](https://aka.ms/azure-support).

## <a name="next-steps"></a>Passos seguintes
* [Diagnosticar e resolver problemas](troubleshoot-dot-net-sdk.md) quando utilizar o Azure Cosmos DB .NET SDK.
* Saiba mais sobre as diretrizes de desempenho para [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) e [.NET v2](performance-tips.md).