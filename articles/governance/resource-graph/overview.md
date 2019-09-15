---
title: Descrição geral do Azure Resource Graph
description: Entenda como o serviço do grafo de recursos do Azure permite consultas complexas de recursos em escala.
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/06/2019
ms.topic: overview
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 6721769b0ab6df4165281d9b5b75c0e1332ed1dc
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/15/2019
ms.locfileid: "71001657"
---
# <a name="overview-of-the-azure-resource-graph-service"></a>Visão geral do serviço de grafo de recursos do Azure

O grafo de recursos do Azure é um serviço no Azure projetado para estender o gerenciamento de recursos do Azure, fornecendo exploração de recursos eficiente e eficaz com a capacidade de consultar em escala em um determinado conjunto de assinaturas para que você possa governar efetivamente seu ambiente. Estas consultas fornecem as seguintes funcionalidades:

- Capacidade de consultar recursos com filtragem, agrupamento e classificação complexos por propriedades de recursos.
- Capacidade de explorar iterativamente os recursos com base nos requisitos de governança.
- Capacidade de avaliar o impacto da aplicação de políticas num vasto ambiente de cloud.
- Capacidade de [detalhar as alterações feitas nas propriedades do recurso](./how-to/get-resource-changes.md) (versão prévia).

Nesta documentação, vamos abordar cada funcionalidade de forma detalhada.

> [!NOTE]
> O grafo de recursos do Azure capacita a barra de pesquisa de portal do Azure, a nova experiência de procurar todos os recursos e a_diferença visual_do [histórico](../policy/how-to/determine-non-compliance.md#change-history-preview)
> de alterações do Azure Policy. Ele foi projetado para ajudar os clientes a gerenciar ambientes em larga escala.

[!INCLUDE [service-provider-management-toolkit](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Como é que o Resource Graph complementa o Azure Resource Manager?

Azure Resource Manager atualmente dá suporte a consultas em campos de recursos básicos, especificamente, nome do recurso, ID, tipo, grupo de recursos, assinatura e local. O Resource Manager também fornece recursos para chamar provedores de recursos individuais para propriedades detalhadas de um recurso por vez.

Com o Azure Resource Graph, pode aceder a estas propriedades devolvidas pelos fornecedores de recursos sem a necessidade de fazer chamadas individuais para cada fornecedor de recursos. Para obter uma lista de tipos de recursos com suporte, procure um **Sim** na tabela [recursos para implantações de modo completo](../../azure-resource-manager/complete-mode-deletion.md) . Uma maneira alternativa de ver os tipos de recursos com suporte é por meio do [navegador de esquema do Azure Resource Graph Explorer](./first-query-portal.md#schema-browser).

Com o grafo de recursos do Azure, você pode:

- Acesse as propriedades retornadas pelos provedores de recursos sem a necessidade de fazer chamadas individuais para cada provedor de recursos.
- Exiba os últimos 14 dias do histórico de alterações feitas ao recurso para ver quais propriedades foram alteradas e quando. (pré-visualização)

## <a name="how-resource-graph-is-kept-current"></a>Como o grafo de recursos é mantido atual

Quando um recurso do Azure é atualizado, o grafo de recursos é notificado pelo Gerenciador de recursos sobre a alteração.
Em seguida, o grafo de recursos atualiza seu banco de dados. O grafo de recursos também faz uma _verificação completa_normal. Essa verificação garante que os dados do grafo de recursos sejam atuais se houver notificações perdidas ou quando um recurso for atualizado fora do Gerenciador de recursos.

## <a name="the-query-language"></a>Linguagem de consulta

Agora que você tem uma compreensão melhor do que é o grafo de recursos do Azure, vamos nos aprofundar em como construir consultas.

É importante entender que a linguagem de consulta do grafo de recursos do Azure é baseada na [linguagem de consulta Kusto](../../data-explorer/data-explorer-overview.md) usada pelo Azure data Explorer.

Em primeiro lugar, para obter detalhes sobre operações e funções que podem ser utilizadas com o Azure Resource Graph, veja [Linguagem de consulta do Resource Graph](./concepts/query-language.md).
Para procurar recursos, veja [explorar recursos](./concepts/explore-resources.md).

## <a name="permissions-in-azure-resource-graph"></a>Permissões no Azure Resource Graph

Para utilizar o Resource Graph, tem de possuir os direitos adequados no [Controlo de acesso baseado em funções](../../role-based-access-control/overview.md) (RBAC) com acesso de leitura, pelo menos, aos recursos que pretende consultar. Sem, pelo menos, permissões `read` para o objeto do Azure ou o grupo de objetos, não seriam devolvidos resultados.

> [!NOTE]
> O grafo de recursos usa as assinaturas disponíveis para uma entidade de segurança durante o logon. Para ver os recursos de uma nova assinatura adicionada durante uma sessão ativa, a entidade de segurança deve atualizar o contexto. Essa ação ocorre automaticamente durante o logout e o retorno.

CLI do Azure e Azure PowerShell usar assinaturas às quais o usuário tem acesso. Ao usar a API REST diretamente, a lista de assinaturas é fornecida pelo usuário. Se o usuário tiver acesso a qualquer uma das assinaturas na lista, os resultados da consulta serão retornados para as assinaturas às quais o usuário tem acesso. Esse comportamento é o mesmo que ao chamar [grupos de recursos-List](/rest/api/resources/resourcegroups/list) \- você obtém os grupos de recursos aos quais você tem acesso sem nenhuma indicação de que o resultado pode ser parcial.
Se não houver nenhuma assinatura na lista de assinaturas à qual o usuário tenha direitos apropriados, a resposta será um _403_ (proibido).

## <a name="throttling"></a>Limitação

Como um serviço gratuito, as consultas ao grafo de recursos são limitadas para fornecer a melhor experiência e o tempo de resposta para todos os clientes. Se sua organização quiser usar o API do Graph de recursos para consultas em grande escala e frequentes, use o portal ' comentários ' na [página do portal do grafo de recursos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/ResourceGraph).
Forneça seu caso comercial e selecione a caixa de seleção ' a Microsoft pode enviar um email sobre seus comentários ' para que a equipe entre em contato com você.

O grafo de recursos limita as consultas no nível de usuário. A resposta do serviço contém os seguintes cabeçalhos HTTP:

- `x-ms-user-quota-remaining`(int): A cota de recursos restante para o usuário. Esse valor é mapeado para a contagem de consultas.
- `x-ms-user-quota-resets-after` (hh:mm:ss): A duração de tempo até que o consumo de cota de um usuário seja redefinido

Para obter mais informações, consulte [diretrizes para solicitações limitadas](./concepts/guidance-for-throttled-requests.md).

## <a name="running-your-first-query"></a>Executar a primeira consulta

O Gerenciador de gráficos de recursos do Azure, parte do portal do Azure, permite executar consultas de grafo de recursos diretamente no portal do Azure. Fixe os resultados como gráficos dinâmicos para fornecer informações dinâmicas em tempo real para o fluxo de trabalho do Portal. Para obter mais informações, consulte [a primeira consulta com o Gerenciador de gráficos de recursos do Azure](first-query-portal.md).

O grafo de recursos dá suporte a CLI do Azure, Azure PowerShell, SDK do Azure para .NET e muito mais. A consulta é estruturada da mesma para cada idioma. Saiba como habilitar o grafo de recursos com:

- [portal do Azure e Gerenciador de grafo de recursos](first-query-portal.md) 
- [CLI do Azure](first-query-azurecli.md#add-the-resource-graph-extension)
- [Azure PowerShell](first-query-powershell.md#add-the-resource-graph-module)

## <a name="next-steps"></a>Passos seguintes

- Execute sua primeira consulta com [CLI do Azure](first-query-azurecli.md).
- Execute sua primeira consulta com [Azure PowerShell](first-query-powershell.md).
- Comece com as [consultas iniciais](./samples/starter.md).
- Aprimore sua compreensão com [consultas avançadas](./samples/advanced.md).