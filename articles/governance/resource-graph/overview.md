---
title: Descrição geral do Azure Resource Graph
description: Entenda como o serviço Azure Resource Graph permite uma consulta complexa de recursos em escala através de subscrições e inquilinos.
ms.date: 03/02/2020
ms.topic: overview
ms.openlocfilehash: 38ead7be09e038b19c390acd9f10e1c0ccf9d858
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240059"
---
# <a name="what-is-azure-resource-graph"></a>O que é o Azure Resource Graph?

O Azure Resource Graph é um serviço no Azure que é projetado para alargar a Gestão de Recursos Azure, fornecendo uma exploração eficiente e performativa de recursos com a capacidade de consultar em escala através de um determinado conjunto de subscrições para que possa efetivamente governar o seu ambiente. Estas consultas fornecem as seguintes funcionalidades:

- Capacidade de consultar recursos com filtragem, agrupamento e classificação complexos por propriedades de recursos.
- Capacidade de explorar iterativamente recursos com base nos requisitos de governação.
- Capacidade de avaliar o impacto da aplicação de políticas num vasto ambiente de cloud.
- Capacidade de [detalhar as alterações feitas às propriedades dos recursos](./how-to/get-resource-changes.md) (pré-visualização).

Nesta documentação, vamos abordar cada funcionalidade de forma detalhada.

> [!NOTE]
> O Azure Resource Graph alimenta a barra de pesquisa do portal Azure, a nova experiência de navegação "Todos os recursos" e a
> _difusão visual_da história da [Azure](../policy/how-to/determine-non-compliance.md#change-history-preview)Policy. É projetado para ajudar os clientes a gerir ambientes em larga escala.

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Como é que o Resource Graph complementa o Azure Resource Manager?

O Azure Resource Manager atualmente suporta consultas sobre campos de recursos básicos, especificamente - Nome de recursos, ID, Tipo, Grupo de Recursos, Subscrição e Localização. O Gestor de Recursos também fornece facilidades para chamar os fornecedores de recursos individuais para propriedades detalhadas um recurso de cada vez.

Com o Azure Resource Graph, pode aceder a estas propriedades devolvidas pelos fornecedores de recursos sem a necessidade de fazer chamadas individuais para cada fornecedor de recursos. Para obter uma lista de tipos de recursos suportados, reveja a tabela e a referência do [tipo de recurso](./reference/supported-tables-resources.md). Uma forma alternativa de ver tipos de recursos suportados é através do [navegador Azure Resource Graph Explorer Schema](./first-query-portal.md#schema-browser).

Com o Gráfico de Recursos Azure, pode:

- Aceda às propriedades devolvidas pelos fornecedores de recursos sem necessidade de fazer chamadas individuais a cada fornecedor de recursos.
- Veja os últimos 14 dias de mudança feitas ao recurso para ver que propriedades mudaram e quando. (pré-visualização)

## <a name="how-resource-graph-is-kept-current"></a>Como o Gráfico de Recursos é mantido atual

Quando um recurso Azure é atualizado, o Resource Graph é notificado pelo Gestor de Recursos da alteração.
O Resource Graph atualiza então a sua base de dados. O Resource Graph também faz uma _varredura completa_regular. Esta digitalização garante que os dados do Graph de Recursos estão atuais se houver notificações perdidas ou quando um recurso é atualizado fora do Gestor de Recursos.

> [!NOTE]
> O Resource `GET` Graph utiliza uma API não pré-visualização mais recente de cada fornecedor de recursos para recolher propriedades e valores. Como resultado, o imóvel esperado pode não estar disponível. Em alguns casos, a versão API utilizada foi ultrapassada para fornecer propriedades mais atuais ou amplamente utilizadas nos resultados. Consulte a [versão Show API para cada](./samples/advanced.md#apiversion) amostra do tipo de recurso para obter uma lista completa no seu ambiente.

## <a name="the-query-language"></a>Linguagem de consulta

Agora que tem uma melhor compreensão do que é o Azure Resource Graph, vamos mergulhar em como construir consultas.

É importante entender que a linguagem de consulta do Azure Resource Graph é baseada na linguagem de [consulta Kusto](../../data-explorer/data-explorer-overview.md) usada pelo Azure Data Explorer.

Em primeiro lugar, para obter detalhes sobre operações e funções que podem ser utilizadas com o Azure Resource Graph, veja [Linguagem de consulta do Resource Graph](./concepts/query-language.md).
Para procurar recursos, veja [explorar recursos](./concepts/explore-resources.md).

## <a name="permissions-in-azure-resource-graph"></a>Permissões no Azure Resource Graph

Para utilizar o Resource Graph, tem de possuir os direitos adequados no [Controlo de acesso baseado em funções](../../role-based-access-control/overview.md) (RBAC) com acesso de leitura, pelo menos, aos recursos que pretende consultar. Sem, pelo menos, permissões `read` para o objeto do Azure ou o grupo de objetos, não seriam devolvidos resultados.

> [!NOTE]
> O Resource Graph utiliza as assinaturas disponíveis para um diretor durante o login. Para ver os recursos de uma nova subscrição adicionadas durante uma sessão ativa, o diretor deve refrescar o contexto. Esta ação ocorre automaticamente ao iniciar sessão e voltar a entrar.

O Azure CLI e o Azure PowerShell utilizam subscrições a que o utilizador tem acesso. Ao utilizar a Rest API diretamente, a lista de subscrição é fornecida pelo utilizador. Se o utilizador tiver acesso a qualquer uma das subscrições da lista, os resultados da consulta são devolvidos para as subscrições a que o utilizador tem acesso. Este comportamento é o mesmo que quando se chama [Grupos](/rest/api/resources/resourcegroups/list) \- de Recursos - Lista a que obtém grupos de recursos a que acede sem qualquer indicação de que o resultado pode ser parcial.
Se não houver subscrições na lista de subscrição a que o utilizador tenha direitos adequados, a resposta é um _403_ (Proibido).

## <a name="throttling"></a>Limitação

Como um serviço gratuito, as consultas ao Resource Graph são estranguladas para proporcionar a melhor experiência e tempo de resposta para todos os clientes. Se a sua organização quiser utilizar a API do Graph graph de recursos para consultas de grande escala e frequentes, utilize o portal 'Feedback' da página do [portal Resource Graph](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/ResourceGraph).
Forneça o seu caso de negócio e selecione a caixa de verificação 'Microsoft pode enviar-lhe um e-mail sobre o seu feedback' para que a equipa o contacte.

O gráfico de recursos acelera as consultas ao nível do utilizador. A resposta do serviço contém os seguintes cabeçalhos HTTP:

- `x-ms-user-quota-remaining`(int): A restante quota de recursos para o utilizador. Este valor mapeia para consulta conta.
- `x-ms-user-quota-resets-after`(hh:mm:s): A duração do tempo até que o consumo de quota de um utilizador seja reposto

Para mais informações, consulte [Orientação para pedidos acelerados](./concepts/guidance-for-throttled-requests.md).

## <a name="running-your-first-query"></a>Executar a primeira consulta

O Azure Resource Graph Explorer, parte do portal Azure, permite a execução de consultas de gráficos de recursos diretamente no portal Azure. Coloque os resultados como gráficos dinâmicos para fornecer informações dinâmicas em tempo real ao fluxo de trabalho do portal. Para mais informações, consulte [A Primeira Consulta com o Explorador de Gráficos](first-query-portal.md)de Recursos Azure .

O Resource Graph suporta o Azure CLI, Azure PowerShell, Azure SDK para .NET, e muito mais. A consulta é estruturada da mesma forma para cada língua. Saiba como ativar o Graph de Recursos com:

- [Portal Azure e Explorador de Gráficos de Recursos](first-query-portal.md) 
- [Azure CLI](first-query-azurecli.md#add-the-resource-graph-extension)
- [Azure PowerShell](first-query-powershell.md#add-the-resource-graph-module)

## <a name="next-steps"></a>Passos seguintes

- Execute a sua primeira consulta utilizando o [portal Azure.](first-query-portal.md)
- Execute a sua primeira consulta com [o Azure CLI.](first-query-azurecli.md)
- Execute a sua primeira consulta com [a Azure PowerShell.](first-query-powershell.md)