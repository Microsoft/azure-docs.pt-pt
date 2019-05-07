---
title: Descrição geral do Azure Resource Graph
description: Compreenda como o serviço de gráfico de recursos do Azure permite consultas complexas de recursos em escala.
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/06/2019
ms.topic: overview
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 45d5cf7c4235d10e136cc96364d52aa4319bbf79
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65137771"
---
# <a name="overview-of-the-azure-resource-graph-service"></a>Descrição geral do serviço de gráfico de recursos do Azure

Gráfico de recursos do Azure é um serviço no Azure que foi desenvolvido para ampliar do Azure Resource Management, fornecendo eficiente e exploração de recursos de alto desempenho com a capacidade de consulta em escala num determinado conjunto de subscrições para que pode efetivamente regem seu ambiente. Estas consultas fornecem as seguintes funcionalidades:

- Capacidade de consultar recursos com filtragem, agrupamento e classificação complexos por propriedades de recursos.
- Capacidade de maneira iterativa explore os recursos com base nos requisitos de governação.
- Capacidade de avaliar o impacto da aplicação de políticas num vasto ambiente de cloud.
- Capacidade de [detalham as alterações feitas às propriedades de recurso](./how-to/get-resource-changes.md) (pré-visualização).

Nesta documentação, vamos abordar cada funcionalidade de forma detalhada.

> [!NOTE]
> Gráfico de recursos do Azure capacita a barra de pesquisa do portal do Azure, o novo procurar "Todos os recursos" experiência e do Azure Policy [histórico de alterações](../policy/how-to/determine-non-compliance.md#change-history-preview)
> _visual diff_. Foi concebido para ajudar os clientes a gerir os ambientes de grande escala.

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Como é que o Resource Graph complementa o Azure Resource Manager?

O Azure Resource Manager suporta atualmente consultas sobre campos de recurso básico, especificamente - recurso nome ID, tipo, grupo de recursos, subscrição e localização. Gestor de recursos também fornece recursos para chamada dos fornecedores de recursos individuais para um recurso de propriedades detalhadas de cada vez.

Com o Azure Resource Graph, pode aceder a estas propriedades devolvidas pelos fornecedores de recursos sem a necessidade de fazer chamadas individuais para cada fornecedor de recursos. Para obter uma lista de tipos de recurso suportados, procure um **Sim** no [recursos para implementações no modo completa](../../azure-resource-manager/complete-mode-deletion.md) tabela.

Com o gráfico de recursos do Azure, pode:

- Acessar as propriedades retornadas por fornecedores de recursos sem a necessidade de fazer chamadas individuais para cada fornecedor de recursos.
- Ver os últimos 14 dias do histórico de alterações feitas para o recurso para ver as propriedades alteradas e quando. (pré-visualização)

## <a name="how-resource-graph-is-kept-current"></a>Como o gráfico de recursos é mantido atualizado

Quando um recurso do Azure é atualizado, o gráfico de recursos é notificado pelo Resource Manager da alteração.
Gráfico de recursos, em seguida, atualiza a respetiva base de dados. Gráfico de recursos também faz um regular _análise completa_. Esta análise garante que os dados de gráfico de recursos estão atualizados em caso de notificações em falta ou quando um recurso for atualizado fora do Resource Manager.

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

Como um serviço gratuito, as consultas para o gráfico de recursos são limitadas para fornecer o melhor experiência e tempo de resposta para todos os clientes. Se a organização pretende utilizar a Graph API do recurso para consultas em grande escala e frequentes, utilize o portal "Comentários" a partir da página de gráfico de recursos. Certifique-se de que forneça o seu caso comercial e selecione a caixa de verificação "Microsoft pode enviar um e-mail sobre os seus comentários" para que a equipe de entrar em contacto consigo.

Gráfico de recursos limita-se ao nível do inquilino. O serviço substitui e define o `x-ms-ratelimit-remaining-tenant-reads` cabeçalho de resposta para indicar a restante consulta disponível por utilizador no inquilino. Gráfico de recursos repõe a quota de 5 em 5 segundos em vez de cada hora. Para obter mais informações, consulte [Gestor de recursos de limitação de pedidos](../../azure-resource-manager/resource-manager-request-limits.md).

## <a name="running-your-first-query"></a>Executar a primeira consulta

Gráfico de recursos oferece suporte a CLI do Azure, o Azure PowerShell e o Azure SDK para .NET. A consulta está estruturada a mesma para cada idioma. Saiba como ativar o Resource Graph na [CLI do Azure](first-query-azurecli.md#add-the-resource-graph-extension) e no [Azure PowerShell](first-query-powershell.md#add-the-resource-graph-module).

## <a name="next-steps"></a>Passos Seguintes

- Executar a sua primeira consulta [CLI do Azure](first-query-azurecli.md).
- Executar a sua primeira consulta [do Azure PowerShell](first-query-powershell.md).
- Começar com [Starter consultas](./samples/starter.md).
- Melhorar a compreensão com [consultas avançadas](./samples/advanced.md).