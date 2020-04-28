---
title: Resolver erros comuns
description: Aprenda a resolver problemas com os vários SDKs enquanto consulta os recursos do Azure com o Azure Resource Graph.
ms.date: 10/18/2019
ms.topic: troubleshooting
ms.openlocfilehash: f881db4f75bcee8c13221717596442ac29a4b1ac
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74303905"
---
# <a name="troubleshoot-errors-using-azure-resource-graph"></a>Erros de resolução de problemas usando o gráfico de recursos do Azure

Pode ter erros ao consultar os recursos do Azure com o Azure Resource Graph. Este artigo descreve vários erros que podem ocorrer e como resolvê-los.

## <a name="finding-error-details"></a>Encontrar detalhes de erro

A maioria dos erros é o resultado de um problema enquanto executa uma consulta com o Azure Resource Graph. Quando uma consulta falha, o SDK fornece detalhes sobre a consulta falhada. Estas informações indicam a questão para que possa ser corrigida e uma consulta posterior tenha sucesso.

## <a name="general-errors"></a>Erros gerais

### <a name="scenario-too-many-subscriptions"></a><a name="toomanysubscription"></a>Cenário: Demasiadas subscrições

#### <a name="issue"></a>Problema

Os clientes com acesso a mais de 1000 subscrições, incluindo subscrições de inquilinos cruzados com [o Farol Azure,](../../../lighthouse/overview.md)não podem recolher dados em todas as subscrições numa única chamada para o Azure Resource Graph.

#### <a name="cause"></a>Causa

Azure CLI e PowerShell apenas avançam as primeiras 1000 subscrições do Azure Resource Graph. O REST API para o Azure Resource Graph aceita um número máximo de subscrições para realizar a consulta.

#### <a name="resolution"></a>Resolução

Pedidos de lote para a consulta com um subconjunto de subscrições para permanecer abaixo do limite de subscrição de 1000. A solução está a utilizar o parâmetro **de subscrição** no PowerShell.

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

### <a name="scenario-unsupported-content-type-rest-header"></a><a name="rest-contenttype"></a>Cenário: Cabeçalho DE REPOUSO DO Tipo de Conteúdo Não Suportado

#### <a name="issue"></a>Problema

Os clientes que consultam o Gráfico de Recursos Azure REST API recebem uma resposta _de 500_ (Erro de Servidor Interno) devolvida.

#### <a name="cause"></a>Causa

A API do gráfico de recursos `Content-Type` azure REST apenas suporta uma **aplicação/json**. Algumas ferramentas rest ou agentes predefinidos para **texto/planície**, que não é suportado pela API REST.

#### <a name="resolution"></a>Resolução

Valide que a ferramenta ou agente que está a usar para `Content-Type` consultar o Gráfico de Recursos Azure tem o cabeçalho REST API configurado para **aplicação/json**.

### <a name="scenario-no-read-permission-to-all-subscriptions-in-list"></a><a name="rest-403"></a>Cenário: Não há autorização de leitura para todas as subscrições da lista

#### <a name="issue"></a>Problema

Os clientes que passam explicitamente uma lista de subscrições com uma consulta do Azure Resource Graph obtêm uma resposta _403_ (Proibida).

#### <a name="cause"></a>Causa

Se o cliente não tiver lido permissão para todas as subscrições fornecidas, o pedido é negado por falta de direitos de segurança adequados.

#### <a name="resolution"></a>Resolução

Inclua pelo menos uma subscrição na lista de subscrição a que o cliente que executa a consulta tenha pelo menos lido acesso. Para mais informações, consulte [Permissões no Gráfico](../overview.md#permissions-in-azure-resource-graph)de Recursos Azure .

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

- Obtenha respostas de especialistas do Azure através dos [Fóruns Azure.](https://azure.microsoft.com/support/forums/)
- Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente, ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.
- Se precisar de mais ajuda, pode apresentar um incidente de apoio ao Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.