---
title: Resolução de problemas Azure Cosmos DB HTTP 408 ou solicitar problemas de timeout com .NET SDK
description: Como diagnosticar e corrigir .NET SDK solicitar exceção de tempo limite
author: j82w
ms.service: cosmos-db
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: a0469feed391025f8dd50a7f8b11b96265b0df29
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/07/2020
ms.locfileid: "87987414"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-net-sdk-request-timeout"></a>Diagnóstico e resolução de problemas Azure Cosmos DB .NET SDK solicitar tempo limite
O erro HTTP 408 ocorre se o SDK não conseguir completar o pedido antes do prazo de tempo ocorrer.

## <a name="customizing-the-timeout-on-the-azure-cosmos-net-sdk"></a>Personalizar o tempo limite no Azure Cosmos .NET SDK

O SDK tem duas alternativas distintas para controlar os intervalos de tempo, cada um com um âmbito diferente.

### <a name="requesttimeout"></a>RequestTimeout

A `CosmosClientOptions.RequestTimeout` configuração (ou `ConnectionPolicy.RequestTimeout` para SDK V2) permite-lhe definir um tempo limite que afeta cada pedido de rede individual.  Uma operação iniciada por um utilizador pode abranger vários pedidos de rede (por exemplo, pode haver estrangulamento) e esta configuração aplicar-se-ia a cada pedido de rede na nova rota. Isto não é o fim do tempo limite de pedido de operação.

### <a name="cancellationtoken"></a>CancelamentoToken

Todas as operações de async no SDK têm um parâmetro de CancelamentoToken opcional. Este [CancelamentoToken](https://docs.microsoft.com/dotnet/standard/threading/how-to-listen-for-cancellation-requests-by-polling) é utilizado durante toda a operação, em todos os pedidos de rede. Entre pedidos de rede, o CancelamentoToken pode ser verificado e uma operação cancelada se o token relacionado estiver expirado. CancelamentoToken deve ser usado para definir um tempo limite esperado aproximadamente no âmbito da operação.

> [!NOTE]
> CancelamentoToken é um mecanismo onde a biblioteca verificará o cancelamento quando [não causará um estado inválido](https://devblogs.microsoft.com/premier-developer/recommended-patterns-for-cancellationtoken/). A operação pode não cancelar exatamente quando a hora definida no cancelamento está acabando, mas, em vez disso, depois de o tempo acabar, irá cancelar quando for seguro fazê-lo.

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas
A lista que se segue contém causas e soluções conhecidas para solicitar exceções no prazo de tempo.

### <a name="1-high-cpu-utilization-most-common-case"></a>1. Alta utilização do CPU (caso mais comum)
Para uma latência ótima, recomenda-se que o uso do CPU seja de cerca de 40%. Recomenda-se a utilização de 10 segundos como intervalo para monitorizar a utilização máxima (não média) do CPU. Os picos de CPU são mais comuns com consultas de partição cruzada onde pode fazer múltiplas ligações para uma única consulta.

#### <a name="solution"></a>Solução:
A aplicação do cliente que utiliza o SDK deve ser dimensionada para cima/para fora.

### <a name="2-socket--port-availability-might-be-low"></a>2. A disponibilidade da tomada /porta pode ser baixa
Ao correr em Azure, os clientes que usam o .NET SDK podem atingir a exaustão da porta Azure SNAT (PAT).

#### <a name="solution-1"></a>Solução 1:
Se estiver a correr em VMs Azure, siga o [guia de exaustão do porto SNAT](troubleshoot-dot-net-sdk.md#snat).

#### <a name="solution-2"></a>Solução 2:
Se estiver a executar o Azure App Service, siga o [guia de resolução de erros de ligação](../app-service/troubleshoot-intermittent-outbound-connection-errors.md#cause) e [utilize diagnósticos do Serviço de Aplicações](https://azure.github.io/AppService/2018/03/01/Deep-Dive-into-TCP-Connections-in-App-Service-Diagnostics.html).

#### <a name="solution-3"></a>Solução 3:
Se utilizar um representante HTTP, certifique-se de que consegue suportar o número de ligações configuradas no SDK `ConnectionPolicy` .
Caso contrário, terá problemas de ligação.

### <a name="3-creating-multiple-client-instances"></a>3. Criação de múltiplas instâncias de clientes
A criação de múltiplas instâncias de clientes pode levar a problemas de contenção de ligação e de tempo limite.

#### <a name="solution"></a>Solução:
Siga as [dicas de desempenho](performance-tips-dotnet-sdk-v3-sql.md#sdk-usage)e use um único exemplo cosmosclient em todo um processo.

### <a name="4-hot-partition-key"></a>4. Chave de partição quente
AZure Cosmos DB distribui a produção global a provisionada uniformemente através de divisórias físicas. Quando há uma partição quente, uma ou mais teclas de partição lógica numa partição física estão a consumir todos os RU/s da partição física, enquanto o RU/s em outras divisórias físicas não é usedado. Como sintoma, o total de RU/s consumidos será inferior ao TOTAL previsto ru/s na base de dados ou contentor, mas ainda verá estrangulamento (429s) nos pedidos contra a chave de partição lógica quente. Utilize a [métrica de consumo de RU normalizada](monitor-normalized-request-units.md) para ver se a carga de trabalho encontra uma divisória quente. 

#### <a name="solution"></a>Solução:
Escolha uma boa chave de partição que distribui uniformemente o volume e armazenamento do pedido. Saiba como mudar a [sua tecla de partição.](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/)

### <a name="5-high-degree-of-concurrency"></a>5. Elevado grau de concordância
A aplicação está a fazer um alto nível de concordância, o que pode levar a uma disputa no canal

#### <a name="solution"></a>Solução:
A aplicação do cliente que utiliza o SDK deve ser dimensionada para cima/para fora.

### <a name="6-large-requests-andor-responses"></a>6. Grandes pedidos e/ou respostas
Grandes pedidos ou respostas podem levar a bloqueios de linha no canal e exacerbar a contenção, mesmo com um grau de concordância relativamente baixo.

#### <a name="solution"></a>Solução:
A aplicação do cliente que utiliza o SDK deve ser dimensionada para cima/para fora.

### <a name="7-failure-rate-is-within-cosmos-db-sla"></a>7. A taxa de avaria está dentro da Cosmos DB SLA
A aplicação deve ser capaz de lidar com falhas transitórias e tentar novamente quando necessário. 408 exceções não são novamente julgadas porque na criação de caminhos não é possível saber se o serviço criou o item ou se não o fez. Enviar o mesmo item novamente para criar irá causar uma exceção de conflito. A lógica de negócio das aplicações dos utilizadores pode ter uma lógica personalizada para lidar com conflitos, o que quebraria a ambiguidade de um item existente vs conflito de uma criação de novamente.

### <a name="8-failure-rate-is-violating-the-cosmos-db-sla"></a>8. A taxa de avaria está a violar o Cosmos DB SLA
Por favor contacte o suporte da Azure.

## <a name="next-steps"></a>Passos seguintes
* [Diagnosticar e resolver problemas](troubleshoot-dot-net-sdk.md) ao utilizar a Azure Cosmos DB .NET SDK
* Conheça as diretrizes de desempenho para [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) e [.NET V2](performance-tips.md)
