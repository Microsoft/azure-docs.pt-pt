---
title: Resolver erros comuns
description: Aprenda a resolver problemas com os vários SDKs enquanto consulta recursos Azure com O Gráfico de Recursos Azure.
ms.date: 10/14/2020
ms.topic: troubleshooting
ms.openlocfilehash: 13c5d5ffde8b0b82fcafa5e8149400555a0b18a6
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92056962"
---
# <a name="troubleshoot-errors-using-azure-resource-graph"></a>Erros de resolução de problemas usando gráfico de recurso Azure

Pode encontrar erros ao consultar os recursos do Azure com o Azure Resource Graph. Este artigo descreve vários erros que podem ocorrer e como resolvê-los.

## <a name="finding-error-details"></a>Encontrar detalhes de erro

A maioria dos erros são o resultado de um problema durante a execução de uma consulta com o Azure Resource Graph. Quando uma consulta falha, o SDK fornece detalhes sobre a consulta falhada. Esta informação indica a questão para que possa ser corrigida e uma consulta posterior seja bem sucedida.

## <a name="general-errors"></a>Erros gerais

### <a name="scenario-throttled-requests"></a><a name="throttled"></a>Cenário: Pedidos de throttled

#### <a name="issue"></a>Problema

Os clientes que fazem consultas de recursos grandes ou frequentes têm pedidos acelerados.

#### <a name="cause"></a>Causa

O Azure Resource Graph atribui um número de quota para cada utilizador com base numa janela de tempo. Por exemplo, um utilizador pode enviar no máximo 15 consultas dentro de cada janela de 5 segundos sem ser estrangulado. O valor do contingente é determinado por muitos fatores e está sujeito a alterações. Para obter mais informações, consulte [Throttling no Azure Resource Graph](../overview.md#throttling).

#### <a name="resolution"></a>Resolução

Existem vários métodos para lidar com pedidos acelerados:

- [Consultas de agrupamento](../concepts/guidance-for-throttled-requests.md#grouping-queries)
- [Escalonamento de consultas](../concepts/guidance-for-throttled-requests.md#staggering-queries)
- [Consulta em Paralelo](../concepts/guidance-for-throttled-requests.md#query-in-parallel)
- [Paginação](../concepts/guidance-for-throttled-requests.md#pagination)

### <a name="scenario-too-many-subscriptions"></a><a name="toomanysubscription"></a>Cenário: Demasiadas subscrições

#### <a name="issue"></a>Problema

Os clientes com acesso a mais de 1000 subscrições, incluindo subscrições de inquilinos cruzadas com [o Azure Lighthouse,](../../../lighthouse/overview.md)não conseguem obter dados em todas as subscrições numa única chamada para o Azure Resource Graph.

#### <a name="cause"></a>Causa

A Azure CLI e PowerShell avançam apenas as primeiras 1000 subscrições do Azure Resource Graph. A API REST para Azure Resource Graph aceita um número máximo de subscrições para realizar a consulta.

#### <a name="resolution"></a>Resolução

Pedidos de lote para a consulta com um subconjunto de subscrições para permanecer abaixo do limite de subscrição 1000. A solução está a utilizar o parâmetro **de Subscrição** no PowerShell.

```azurepowershell-interactive
# Replace this query with your own
$query = 'Resources | project type'

# Fetch the full array of subscription IDs
$subscriptions = Get-AzSubscription
$subscriptionIds = $subscriptions.Id

# Create a counter, set the batch size, and prepare a variable for the results
$counter = [PSCustomObject] @{ Value = 0 }
$batchSize = 1000
$response = @()

# Group the subscriptions into batches
$subscriptionsBatch = $subscriptionIds | Group -Property { [math]::Floor($counter.Value++ / $batchSize) }

# Run the query for each batch
foreach ($batch in $subscriptionsBatch){ $response += Search-AzGraph -Query $query -Subscription $batch.Group }

# View the completed results of the query on all subscriptions
$response
```

### <a name="scenario-unsupported-content-type-rest-header"></a><a name="rest-contenttype"></a>Cenário: Cabeçalho de descanso do tipo de conteúdo não suportado

#### <a name="issue"></a>Problema

Os clientes que consultam o Azure Resource Graph REST API obtêm uma resposta _de 500_ (Erro do Servidor Interno) devolvida.

#### <a name="cause"></a>Causa

A Azure Resource Graph REST API suporta apenas uma `Content-Type` **aplicação/json**. Algumas ferramentas ou agentes REST por defeito de **texto/planície,** que não é suportado pela API REST.

#### <a name="resolution"></a>Resolução

Valide que a ferramenta ou agente que está a utilizar para consultar o Azure Resource Graph tem o cabeçalho REST API `Content-Type` configurado para **aplicação/json**.

### <a name="scenario-no-read-permission-to-all-subscriptions-in-list"></a><a name="rest-403"></a>Cenário: Não leia autorização para todas as subscrições na lista

#### <a name="issue"></a>Problema

Os clientes que passam explicitamente uma lista de subscrições com uma consulta de Gráfico de Recursos Azure obtêm uma resposta _403_ (Proibida).

#### <a name="cause"></a>Causa

Se o cliente não tiver lido permissão para todas as subscrições fornecidas, o pedido é negado por falta de direitos de segurança adequados.

#### <a name="resolution"></a>Resolução

Inclua pelo menos uma subscrição na lista de subscrição a que o cliente que executa a consulta tem pelo menos acesso lido. Para obter mais informações, consulte [permissões no Gráfico de Recursos Azure](../overview.md#permissions-in-azure-resource-graph).

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

- Obtenha respostas de especialistas da Azure através dos [Fóruns Azure.](https://azure.microsoft.com/support/forums/)
- Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.
- Se precisar de mais ajuda, pode apresentar um incidente de apoio ao Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter **Apoio**.