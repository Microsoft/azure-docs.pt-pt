---
title: Descrição geral do Azure Resource Graph
description: Compreenda como o serviço Azure Resource Graph permite uma consulta complexa de recursos em escala através de subscrições e inquilinos.
ms.date: 10/14/2020
ms.topic: overview
ms.openlocfilehash: 8e61dadc44a2b07066f7bac761c366c746cef1f1
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92057778"
---
# <a name="what-is-azure-resource-graph"></a>O que é o Azure Resource Graph?

O Azure Resource Graph é um serviço em Azure que é projetado para alargar a Gestão de Recursos Azure, fornecendo uma exploração eficiente e performante de recursos com a capacidade de consultar em escala através de um determinado conjunto de subscrições para que possa governar eficazmente o seu ambiente. Estas consultas fornecem as seguintes funcionalidades:

- Capacidade de consultar recursos com filtragem, agrupamento e classificação complexos por propriedades de recursos.
- Capacidade de explorar iterativamente recursos com base nos requisitos de governação.
- Capacidade de avaliar o impacto da aplicação de políticas num vasto ambiente de cloud.
- Capacidade de [detalhar as alterações feitas às propriedades dos recursos](./how-to/get-resource-changes.md) (pré-visualização).

Nesta documentação, vamos abordar cada funcionalidade de forma detalhada.

> [!NOTE]
> O Azure Resource Graph alimenta a barra de pesquisa do portal Azure, a nova experiência de navegação de 'Todos os recursos' e a história visual de [Mudança](../policy/how-to/determine-non-compliance.md#change-history)da Azure 
>  _visual diff_Policy. É projetado para ajudar os clientes a gerir ambientes de grande escala.

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Como é que o Resource Graph complementa o Azure Resource Manager?

Atualmente, o Gestor de Recursos suporta consultas sobre campos básicos de recursos, especificamente - Nome de recursos, ID, Tipo, Grupo de Recursos, Subscrição e Localização. O Gestor de Recursos também fornece facilidades para chamar fornecedores individuais de recursos para propriedades detalhadas um recurso de cada vez.

Com o Azure Resource Graph, pode aceder a estas propriedades devolvidas pelos fornecedores de recursos sem a necessidade de fazer chamadas individuais para cada fornecedor de recursos. Para obter uma lista de tipos de recursos suportados, reveja a [tabela e a referência do tipo de recurso](./reference/supported-tables-resources.md). Uma forma alternativa de ver tipos de recursos suportados é através do [navegador Azure Resource Graph Explorer Schema](./first-query-portal.md#schema-browser).

Com o Azure Resource Graph, pode:

- Aceda às propriedades devolvidas pelos fornecedores de recursos sem necessidade de fazer chamadas individuais a cada fornecedor de recursos.
- Veja os últimos 14 dias de história de mudança feita ao recurso para ver que propriedades mudaram e quando. (pré-visualização)

> [!NOTE]
> Como uma funcionalidade _de pré-visualização,_ `type` alguns objetos têm propriedades adicionais não-Resource Manager disponíveis. Para obter mais informações, consulte [propriedades estendidas (pré-visualização)](./concepts/query-language.md#extended-properties).

## <a name="how-resource-graph-is-kept-current"></a>Como o Gráfico de Recursos é mantido atual

Quando um recurso Azure é atualizado, o Gráfico de Recursos é notificado pelo Gestor de Recursos da alteração.
O Gráfico de Recursos atualiza então a sua base de dados. O Gráfico de Recursos também faz uma _varredura completa_regular. Esta verificação garante que os dados do Gráfico de Recurso estão em vigor se houver notificações perdidas ou quando um recurso for atualizado fora do Gestor de Recursos.

> [!NOTE]
> O Resource Graph utiliza uma `GET` a mais recente API não pré-visualização de cada fornecedor de recursos para recolher propriedades e valores. Como resultado, o imóvel esperado pode não estar disponível. Em alguns casos, a versão API utilizada foi ultrapassada para fornecer propriedades mais atuais ou amplamente utilizadas nos resultados. Consulte a [versão Show API para cada](./samples/advanced.md#apiversion) amostra do tipo de recurso para obter uma lista completa no seu ambiente.

## <a name="the-query-language"></a>Linguagem de consulta

Agora que tem uma melhor compreensão do que é o Azure Resource Graph, vamos mergulhar em como construir consultas.

É importante entender que a linguagem de consulta do Azure Resource Graph é baseada na [linguagem de consulta Kusto](/azure/data-explorer/data-explorer-overview) usada pelo Azure Data Explorer.

Em primeiro lugar, para obter detalhes sobre operações e funções que podem ser utilizadas com o Azure Resource Graph, veja [Linguagem de consulta do Resource Graph](./concepts/query-language.md). Para procurar recursos, veja [explorar recursos](./concepts/explore-resources.md).

## <a name="permissions-in-azure-resource-graph"></a>Permissões no Azure Resource Graph

Para utilizar o Gráfico de Recursos, tem de ter direitos adequados no controlo de acesso baseado em [funções (Azure RBAC)](../../role-based-access-control/overview.md) com, pelo menos, o acesso aos recursos que pretende consultar. Sem, pelo menos, permissões `read` para o objeto do Azure ou o grupo de objetos, não seriam devolvidos resultados.

> [!NOTE]
> O Gráfico de Recursos utiliza as subscrições disponíveis para um principal durante o início de sessão. Para ver os recursos de uma nova subscrição adicionadas durante uma sessão ativa, o diretor deve atualizar o contexto. Esta ação acontece automaticamente quando se inicia e volta a entrar.

A Azure CLI e Azure PowerShell utilizam subscrições a que o utilizador tem acesso. Ao utilizar diretamente a API REST, a lista de subscrições é fornecida pelo utilizador. Se o utilizador tiver acesso a qualquer uma das subscrições da lista, os resultados da consulta são devolvidos para as subscrições a que o utilizador tem acesso. Este comportamento é o mesmo que quando liga para [grupos de recursos - Liste](/rest/api/resources/resourcegroups/list) \- que obtém grupos de recursos a que tem acesso sem qualquer indicação de que o resultado pode ser parcial. Se não houver subscrições na lista de subscrições a que o utilizador tem os direitos adequados, a resposta é uma _403_ (Proibida).

> [!NOTE]
> Na versão **pré-visualização** rest `2020-04-01-preview` API, a lista de subscrições pode ser omitida.
> Quando os `subscriptions` `managementGroupId` imóveis e os imóveis não estão definidos no pedido, o _âmbito_ é definido para o arrendatário. Para mais informações, consulte [o âmbito da consulta.](./concepts/query-language.md#query-scope)

## <a name="throttling"></a>Limitação

Como um serviço gratuito, as consultas ao Resource Graph são aceleradas para proporcionar a melhor experiência e tempo de resposta para todos os clientes. Se a sua organização quiser utilizar a API do Gráfico de Recursos para consultas em larga escala e frequentes, utilize o portal 'Feedback' a partir da página do [portal Resource Graph](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/ResourceGraph).
Forneça o seu caso de negócio e selecione a caixa de verificação 'Microsoft pode enviar-lhe um e-mail sobre o seu feedback' para que a equipa o contacte.

Consultas de aceleração de gráficos de recurso ao nível do utilizador. A resposta de serviço contém os seguintes cabeçalhos HTTP:

- `x-ms-user-quota-remaining` (int): A restante quota de recursos para o utilizador. Este valor mapeia para a contagem de consultas.
- `x-ms-user-quota-resets-after` (hh:mm:ss): A duração do tempo até ao reinício do consumo de quota de um utilizador

Para obter mais informações, consulte [Orientação para pedidos de aceleração.](./concepts/guidance-for-throttled-requests.md)

## <a name="running-your-first-query"></a>Executar a primeira consulta

O Azure Resource Graph Explorer, parte do portal Azure, permite a execução de consultas de Gráfico de Recursos diretamente no portal Azure. Pin os resultados como gráficos dinâmicos para fornecer informações dinâmicas em tempo real ao seu fluxo de trabalho do portal. Para obter mais informações, consulte [primeira consulta com o Azure Resource Graph Explorer](./first-query-portal.md).

O Resource Graph suporta Azure CLI, Azure PowerShell, Azure SDK para Python, e muito mais. A consulta é estruturada da mesma forma para cada língua. Saiba como ativar o Gráfico de Recursos com:

- [Portal Azure e Explorador de Gráficos de Recursos](./first-query-portal.md) 
- [CLI do Azure](./first-query-azurecli.md#add-the-resource-graph-extension)
- [Azure PowerShell](./first-query-powershell.md#add-the-resource-graph-module)
- [Python](./first-query-python.md#add-the-resource-graph-library)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a [língua de consulta.](./concepts/query-language.md)
- Consulte o idioma em uso nas [consultas de arranque](./samples/starter.md).
- Consulte utilizações avançadas em [consultas avançadas.](./samples/advanced.md)
