---
title: Resolver erros comuns
description: Saiba como solucionar problemas de consulta de recursos do Azure com o grafo de recursos do Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/18/2019
ms.topic: troubleshooting
ms.service: resource-graph
ms.openlocfilehash: 030fe26a0aa8fc4ed855fb7744e576366f4fd2e2
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389691"
---
# <a name="troubleshoot-errors-using-azure-resource-graph"></a>Solucionar erros usando o grafo de recursos do Azure

Você pode encontrar erros ao consultar recursos do Azure com o grafo de recursos do Azure. Este artigo descreve vários erros que podem ocorrer e como resolvê-los.

## <a name="finding-error-details"></a>Localizando detalhes do erro

A maioria dos erros é o resultado de um problema durante a execução de uma consulta com o grafo de recursos do Azure. Quando uma consulta falha, o SDK fornece detalhes sobre a consulta com falha. Essas informações indicam o problema para que ele possa ser corrigido e uma consulta posterior seja realizada com sucesso.

## <a name="general-errors"></a>Erros gerais

### <a name="toomanysubscription"></a>Cenário: muitas assinaturas

#### <a name="issue"></a>Problema

Os clientes com acesso a mais de 1000 assinaturas, incluindo assinaturas entre locatários com o [Azure Lighthouse](../../../lighthouse/overview.md), não podem buscar dados em todas as assinaturas em uma única chamada para o grafo de recursos do Azure.

#### <a name="cause"></a>Causa

CLI do Azure e o PowerShell encaminham apenas as primeiras assinaturas 1000 para o grafo de recursos do Azure. A API REST para o grafo de recursos do Azure aceita um número máximo de assinaturas para executar a consulta.

#### <a name="resolution"></a>Resolução

Solicitações em lote para a consulta com um subconjunto de assinaturas para permanecer sob o limite de assinatura 1000. A solução está usando o parâmetro de **assinatura** no PowerShell.

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

### <a name="rest-contenttype"></a>Cenário: cabeçalho REST de tipo de conteúdo sem suporte

#### <a name="issue"></a>Problema

Os clientes que consultam a API REST do grafo de recursos do Azure obtêm uma resposta _500_ (erro interno do servidor) retornada.

#### <a name="cause"></a>Causa

A API REST do grafo de recursos do Azure só dá suporte a um `Content-Type` de **Application/JSON**. Algumas ferramentas REST ou agentes assumem o padrão de **text/plain**, que não é suportado pela API REST.

#### <a name="resolution"></a>Resolução

Valide se a ferramenta ou o agente que você está usando para consultar o grafo de recursos do Azure tem o cabeçalho da API REST `Content-Type` configurado para **Application/JSON**.

### <a name="rest-403"></a>Cenário: nenhuma permissão de leitura para todas as assinaturas na lista

#### <a name="issue"></a>Problema

Os clientes que passam explicitamente uma lista de assinaturas com uma consulta do grafo de recursos do Azure obtêm uma resposta de _403_ (proibido).

#### <a name="cause"></a>Causa

Se o cliente não tiver permissão de leitura para todas as assinaturas fornecidas, a solicitação será negada devido à falta de direitos de segurança apropriados.

#### <a name="resolution"></a>Resolução

Inclua pelo menos uma assinatura na lista de assinaturas que o cliente que está executando a consulta tenha pelo menos acesso de leitura ao. Para obter mais informações, consulte [permissões no grafo de recursos do Azure](../overview.md#permissions-in-azure-resource-graph).

## <a name="next-steps"></a>Passos seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

- Obtenha respostas de especialistas do Azure por meio dos [fóruns do Azure](https://azure.microsoft.com/support/forums/).
- Ligue-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente ao ligar a comunidade do Azure aos recursos certos: respostas, suporte e especialistas.
- Se precisar de mais ajuda, você poderá arquivar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**.