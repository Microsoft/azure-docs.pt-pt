---
title: Descrição geral do Azure Resource Graph
description: Compreenda como o serviço de gráfico de recursos do Azure permite consultas complexas de recursos em escala.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/30/2019
ms.topic: overview
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: d76a5b32403bd14f18181580f891925130808922
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622801"
---
# <a name="overview-of-the-azure-resource-graph-service"></a>Descrição geral do serviço de gráfico de recursos do Azure

O Azure Resource Graph é um serviço no Azure que foi desenvolvido para ampliar o Azure Resource Management, ao fornecer exploração de recursos eficiente e de alto desempenho, com a capacidade de consultar à escala todas as subscrições e grupos de gestão para que possa controlar de forma eficiente o seu ambiente. Estas consultas fornecem as seguintes funcionalidades:

- Capacidade de consultar recursos com filtragem, agrupamento e classificação complexos por propriedades de recursos.
- Capacidade de explorar de maneira iterativa os recursos com base nos requisitos de governação e de converter a expressão resultante numa definição de política.
- Capacidade de avaliar o impacto da aplicação de políticas num vasto ambiente de cloud.
- Capacidade de [detalham as alterações feitas às propriedades de recurso](./how-to/get-resource-changes.md) (pré-visualização).

Nesta documentação, vamos abordar cada funcionalidade de forma detalhada.

> [!NOTE]
> Gráfico de recursos do Azure é utilizado por procurar nova experiência de "Todos os recursos" do portal do Azure e a Azure Policy [histórico de alterações](../policy/how-to/determine-non-compliance.md#change-history-preview).
> _Visual diff_. Foi concebido para ajudar os clientes a gerir os ambientes de grande escala.

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Como é que o Resource Graph complementa o Azure Resource Manager?

Atualmente, o Azure Resource Manager envia dados para uma cache de recursos limitados que disponibiliza vários campos de recursos, mais especificamente: Nome do recurso, ID, Tipo, Grupo de Recursos, Subscrições e Localização. Antes, trabalhar com mais propriedades de recursos implicava fazer chamadas para cada fornecedor de recursos individuais e pedir os detalhes das propriedades de cada recurso.

Com o Azure Resource Graph, pode aceder a estas propriedades devolvidas pelos fornecedores de recursos sem a necessidade de fazer chamadas individuais para cada fornecedor de recursos. Para obter uma lista de tipos de recurso suportados, procure um **Sim** no [recursos para implementações no modo completa](../../azure-resource-manager/complete-mode-deletion.md) tabela.

Com o gráfico de recursos do Azure, pode:

- Acessar as propriedades retornadas por fornecedores de recursos sem a necessidade de fazer chamadas individuais para cada fornecedor de recursos.
- Ver os últimos 14 dias do histórico de alterações feitas para o recurso para ver as propriedades alteradas e quando. (pré-visualização)

## <a name="the-query-language"></a>Linguagem de consulta

Agora que compreende melhor o que é o Azure Resource Graph, vamos aprofundar como criar consultas.

É importante compreender que a linguagem de consulta do gráfico de recursos do Azure se baseia a [linguagem de consulta de Kusto](../../data-explorer/data-explorer-overview.md) utilizado pelo Explorador de dados do Azure.

Em primeiro lugar, para obter detalhes sobre operações e funções que podem ser utilizadas com o Azure Resource Graph, veja [Linguagem de consulta do Resource Graph](./concepts/query-language.md).
Para procurar recursos, veja [explorar recursos](./concepts/explore-resources.md).

## <a name="permissions-in-azure-resource-graph"></a>Permissões no Azure Resource Graph

Para utilizar o Resource Graph, tem de possuir os direitos adequados no [Controlo de acesso baseado em funções](../../role-based-access-control/overview.md) (RBAC) com acesso de leitura, pelo menos, aos recursos que pretende consultar. Sem, pelo menos, permissões `read` para o objeto do Azure ou o grupo de objetos, não seriam devolvidos resultados.

> [!NOTE]
> Gráfico de recursos usa as subscrições disponíveis para uma entidade de segurança durante o início de sessão. Para ver os recursos de uma nova subscrição adicionada durante uma sessão ativa, o principal tem de atualizar o contexto. Esta ação ocorre automaticamente quando terminar sessão e voltar a.

## <a name="throttling"></a>Limitação

Consultas para o gráfico de recursos são limitadas para fornecer o melhor experiência e tempo de resposta para todos os clientes. Se a organização pretende utilizar a Graph API do recurso para consultas em grande escala e frequentes, utilize portal "Comentários" a partir da página de gráfico de recursos. Certifique-se de que forneça o seu caso comercial e selecione a caixa de verificação "Microsoft pode enviar um e-mail sobre os seus comentários" para que a equipe de entrar em contacto consigo.

## <a name="running-your-first-query"></a>Executar a primeira consulta

Gráfico de recursos oferece suporte a CLI do Azure, o Azure PowerShell e o Azure SDK para .NET. A consulta está estruturada a mesma para cada idioma. Saiba como ativar o Resource Graph na [CLI do Azure](first-query-azurecli.md#add-the-resource-graph-extension) e no [Azure PowerShell](first-query-powershell.md#add-the-resource-graph-module).

## <a name="next-steps"></a>Passos Seguintes

- Executar a sua primeira consulta [CLI do Azure](first-query-azurecli.md).
- Executar a sua primeira consulta [do Azure PowerShell](first-query-powershell.md).
- Começar com [Starter consultas](./samples/starter.md).
- Melhorar a compreensão com [consultas avançadas](./samples/advanced.md).