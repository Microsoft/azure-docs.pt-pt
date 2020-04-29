---
title: Ver e gerir os clientes e os recursos delegados
description: Como prestador de serviços utilizando a gestão de recursos delegados do Azure, pode ver todos os seus recursos e subscrições de clientes delegados, indo para os meus clientes no portal Azure.
ms.date: 01/22/2020
ms.topic: conceptual
ms.openlocfilehash: 0d4b3187066754e8a549f029623762df539b30b1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76543431"
---
# <a name="view-and-manage-customers-and-delegated-resources"></a>Ver e gerir os clientes e os recursos delegados

Os prestadores de serviços que utilizam a [gestão de recursos delegados do Azure](../concepts/azure-delegated-resource-management.md) podem utilizar a página **dos meus clientes** no portal [Azure](https://portal.azure.com) para ver os recursos e subscrições dos clientes delegados. Enquanto nos referimos a prestadores de serviços e clientes aqui, as empresas que gerem vários inquilinos podem usar o mesmo processo para consolidar a sua experiência de gestão.

Para aceder à página dos **meus clientes** no portal Azure, selecione Todos **os serviços,** depois procure os **meus clientes** e selecione-o. Também pode encontrá-lo entrando em "Meus clientes" na caixa de pesquisa perto do topo do portal Azure.

Tenha em mente que a **secção** de clientes topo da página dos **meus clientes** apenas mostra informações sobre clientes que tenham delegado sionárias ou grupos de recursos. Se trabalhar com outros clientes (como através do [programa Cloud Solution Provider,](https://docs.microsoft.com/partner-center/csp-overview)não verá informações sobre esses clientes na secção **Clientes,** a menos que tenha a bordo os seus recursos para a gestão de recursos delegados da Azure.

Mais abaixo na página, uma secção separada chamada **Cloud Solution Provider (Preview)** mostra informações e recursos de faturação para os seus clientes CSP que assinaram o Contrato de [Cliente da Microsoft (MCA)](https://docs.microsoft.com/partner-center/confirm-customer-agreement) e estão [ao abrigo do plano Azure](https://docs.microsoft.com/partner-center/azure-plan-get-started). Para mais informações, consulte Começar com a sua conta de [faturação do Microsoft Partner Agreement](../../billing/mpa-overview.md). Note que estes clientes cSP aparecem nesta secção se você também os abordou para a gestão de recursos delegados do Azure. Da mesma forma, um cliente CSP não tem de aparecer na secção **Cloud Solution Provider (Preview)** dos **meus clientes** para que possa embarcar para a gestão de recursos delegados do Azure.

> [!NOTE]
> Os seus clientes podem ver informações sobre prestadores de serviços navegando para prestadores de **serviços** no portal Azure. Para mais informações, consulte [O View e gereos prestadores de serviços.](view-manage-service-providers.md)

## <a name="view-and-manage-customer-details"></a>Ver e gerir os detalhes do cliente

Para ver os detalhes do cliente, selecione **Clientes** do lado esquerdo da página **dos meus clientes.**

Para cada cliente, você verá o nome do cliente, id do cliente (ID do inquilino), e a oferta associada ao compromisso. Na coluna **delegações,** verá o número de assinaturas delegadas e/ou o número de grupos de recursos delegados.

> [!IMPORTANT]
> Para ver uma delegação, os utilizadores devem ter recebido o papel [de Leitor](../../role-based-access-control/built-in-roles.md#reader) (ou outra função incorporada que inclui o acesso do Leitor) no processo de embarque.

Os filtros no topo da página permitem-lhe classificar e agrupar as informações do seu cliente ou filtrar por clientes, ofertas ou palavras-chave específicas.

Pode ver as seguintes informações desta página:

- Para ver todas as subscrições, ofertas e delegações associadas a um cliente, selecione o nome do cliente.
- Para ver mais detalhes sobre uma oferta e suas delegações, selecione o nome da oferta.
- Para ver mais detalhes sobre atribuições de funções para subscrições delegadas ou grupos de recursos, selecione a entrada na coluna **delegações.**

## <a name="view-and-manage-delegations"></a>Ver e gerir delegações

As delegações mostram o grupo de subscrição/recursos que foi delegado, juntamente com os utilizadores e permissões que têm acesso ao mesmo. Para ver esta informação, selecione **Delegações** do lado esquerdo da página **dos meus clientes.**

Os filtros no topo da página permitem-lhe classificar e agrupar as suas informações de atribuição de acesso ou filtrar por clientes, ofertas ou palavras-chave específicas.

### <a name="view-role-assignments"></a>Ver atribuições de funções

Os utilizadores e permissões associados a cada delegação aparecem na coluna **de atribuições de funções.** Pode selecionar cada entrada para visualizar a lista completa de utilizadores, grupos e diretores de serviço que tenham tido acesso ao grupo de subscrição ou recursos. A partir daí, pode selecionar um determinado nome principal de utilizador, grupo ou serviço para obter mais detalhes.

### <a name="remove-delegations"></a>Remover delegações

Se incluiu utilizadores com a Função de Eliminação de Serviços [Geridos](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) ao embarcar num cliente para a gestão de recursos delegados do Azure, esses utilizadores podem remover uma delegação selecionando o ícone do caixote do lixo que aparece na fila para essa delegação. Quando o fizerem, nenhum utilizador do inquilino do prestador de serviços poderá aceder aos recursos que tinham sido previamente delegados.


## <a name="work-in-the-context-of-a-delegated-subscription"></a>Trabalho no contexto de uma subscrição delegada

Pode trabalhar diretamente no contexto de uma subscrição delegada dentro do portal Azure, sem mudar o diretório em que está a trabalhar. Para tal:

1. Selecione o ícone **de Assinatura Diretório +** perto do topo do portal Azure.
2. No filtro de **subscrição Global,** certifique-se de que apenas a caixa para essa subscrição delegada é selecionada. Pode utilizar a caixa de entrega de **diretórios atual + delegada** para mostrar apenas subscrições dentro de um diretório específico. (Não utilize a opção de **diretório Switch,** uma vez que altera o diretório em que está inscrito.)

Se aceder a um serviço que suporta [experiências de gestão de inquilinos cruzados,](../concepts/cross-tenant-management-experience.md)o serviço será indefinido no contexto da subscrição delegada que selecionou. Pode alterá-lo seguindo os passos acima e verificando todas as caixas **(ou** escolhendo uma ou mais subscrições para trabalhar em vez disso).

> [!NOTE]
> Se lhe foi concedido acesso a um ou mais grupos de recursos, em vez de ter acesso a uma subscrição inteira, pode selecionar a subscrição a que esse grupo de recursos pertence. Em seguida, trabalhará no contexto dessa subscrição, mas só poderá aceder aos grupos de recursos designados.

Também pode aceder a funcionalidades relacionadas com subscrições ou grupos de recursos delegados de dentro de serviços que suportam experiências de gestão de inquilinos cruzados, selecionando o grupo de subscrição ou recursos a partir desse serviço.

## <a name="next-steps"></a>Passos seguintes

- Conheça [as experiências de gestão de inquilinos cruzados.](../concepts/cross-tenant-management-experience.md)
- Saiba como os seus clientes podem [ver e gerir os prestadores](view-manage-service-providers.md) de serviços indo para prestadores de **serviços** no portal Azure.
