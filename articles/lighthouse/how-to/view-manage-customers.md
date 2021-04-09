---
title: Ver e gerir clientes e recursos delegados no portal Azure
description: Como prestador de serviços ou empresa que utiliza o Azure Lighthouse, pode visualizar todos os seus recursos e subscrições delegados indo para os meus clientes no portal Azure.
ms.date: 03/12/2021
ms.topic: how-to
ms.openlocfilehash: 78344015ee027b9844b6339fa7cd95d348488a54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103419335"
---
# <a name="view-and-manage-customers-and-delegated-resources-in-the-azure-portal"></a>Ver e gerir clientes e recursos delegados no portal Azure

Os prestadores de serviços que utilizam o [Farol Azure](../overview.md) podem utilizar a página **My customers** no [portal Azure](https://portal.azure.com) para visualizar os recursos e subscrições delegados do cliente.

> [!TIP]
> Enquanto nos referimos a prestadores de serviços e clientes aqui, [as empresas que gerem vários inquilinos](../concepts/enterprise.md) podem usar o mesmo processo para consolidar a sua experiência de gestão.

Para aceder à página **Dos Meus clientes** no portal Azure, selecione **Todos os serviços,** em seguida, procure **os meus clientes** e selecione-o. Também pode encontrá-lo inserindo "Os meus clientes" na caixa de pesquisa perto do topo do portal Azure.

Tenha em mente que a secção de **clientes** de topo da página **dos meus clientes** apenas mostra informações sobre clientes que delegaram subscrições ou grupos de recursos ao seu inquilino Azure Ative (Azure AD) através do Farol Azure. Se trabalhar com outros clientes (como através do [programa Cloud Solution Provider (CSP),](/partner-center/csp-overview)não verá informações sobre esses clientes na secção **Clientes,** a menos que tenha [acedido aos seus recursos para o Farol Azure](onboard-customer.md) (embora possa ver detalhes sobre certos clientes CSP na [secção Cloud Solution Provider (Preview)](#cloud-solution-provider-preview) mais abaixo na página).

> [!NOTE]
> Os seus clientes podem ver informações sobre fornecedores de serviços navegando para **os prestadores** de serviços no portal Azure. Para mais informações, consulte [Ver e gerir os prestadores de serviços.](view-manage-service-providers.md)

## <a name="view-and-manage-customer-details"></a>Ver e gerir detalhes do cliente

Para ver os dados do cliente, selecione **Clientes** no lado esquerdo da página **dos meus clientes.**

> [!IMPORTANT]
> Para ver estas informações, os utilizadores devem ter recebido a função [Reader](../../role-based-access-control/built-in-roles.md#reader) (ou outra função incorporada que inclua o acesso ao Leitor) no processo de embarque.

Para cada cliente, você verá o nome do cliente, ID do cliente (ID do inquilino) e a versão **De ID** e **Oferta** associada ao noivado. Na coluna **delegações,** verá o número de assinaturas delegadas e/ou o número de grupos de recursos delegados.

As opções no topo da página permitem-lhe classificar, filtrar e agrupar as informações do seu cliente por clientes, ofertas ou palavras-chave específicas.

Pode ver as seguintes informações a partir desta página:

- Para ver todas as subscrições, ofertas e delegações associadas a um cliente, selecione o nome do cliente.
- Para ver mais detalhes sobre uma oferta e suas delegações, selecione o nome da oferta.
- Para ver mais detalhes sobre atribuições de funções para subscrições delegadas ou grupos de recursos, selecione a entrada na coluna **delegações.**

## <a name="view-and-manage-delegations"></a>Ver e gerir delegações

As delegações mostram a subscrição ou grupo de recursos que foi delegado, juntamente com os utilizadores e permissões que têm acesso à sua. Para ver esta informação, selecione **Delegações** no lado esquerdo da página **dos meus clientes.**

As opções no topo da página permitem-lhe classificar, filtrar e agrupar esta informação por clientes específicos, ofertas ou palavras-chave.

### <a name="view-role-assignments"></a>Ver atribuições de funções

Os utilizadores e permissões associadas a cada delegação aparecem na coluna **de atribuições de funções.** Pode selecionar cada entrada para ver a lista completa de utilizadores, grupos e principais serviços a quem foi concedido acesso à subscrição ou grupo de recursos. A partir daí, pode selecionar um determinado nome principal de utilizador, grupo ou serviço para obter mais detalhes.

### <a name="remove-delegations"></a>Remover delegações

Se incluiu utilizadores com a [Atribuição de Registo de Serviços Geridos Eliminar Função](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) ao embarcar um cliente para o Farol de Azure, esses utilizadores podem remover uma delegação selecionando o ícone do caixote do lixo que aparece na fila para essa delegação. Quando o fizerem, nenhum utilizadores do arrendatário do prestador de serviços poderá aceder aos recursos que tinham sido previamente delegados.

Para mais informações, consulte [Remover o acesso a uma delegação.](remove-delegation.md)

## <a name="view-delegation-change-activity"></a>Ver atividade de mudança de delegação

A secção de registo de **atividades** da página **dos meus clientes** acompanha sempre que as subscrições de clientes ou grupos de recursos são delegados ao seu inquilino, e sempre que os recursos previamente delegados são removidos. Estas informações só podem ser visualizadas por utilizadores que tenham sido [atribuídos a função de Leitor de Monitorização no âmbito raiz](monitor-delegation-changes.md).

Para mais informações, consulte [as alterações da delegação no portal Azure.](monitor-delegation-changes.md#view-delegation-changes-in-the-azure-portal)

## <a name="work-in-the-context-of-a-delegated-subscription"></a>Trabalho no contexto de uma assinatura delegada

Pode trabalhar diretamente no contexto de uma subscrição delegada dentro do portal Azure, sem mudar o diretório em que se inscreveu. Para tal:

1. Selecione o ícone **de inscrição + Diretório** perto do topo do portal Azure.
2. No **filtro de subscrição Predefinido,** certifique-se de que apenas a caixa para essa subscrição delegada é selecionada. Pode utilizar a caixa de entrega de **diretórios Current + delegada** para mostrar apenas subscrições dentro de um diretório específico. (Não utilize a opção **de diretório da Switch,** uma vez que altera o diretório ao qual está inscrito.)

Se aceder a um serviço que suporta [experiências de gestão de inquilinos cruzados,](../concepts/cross-tenant-management-experience.md)o serviço irá desresusar o contexto da subscrição delegada que selecionou. Pode alterá-lo seguindo os passos acima e verificando a caixa **Select all** (ou escolhendo uma ou mais subscrições para trabalhar em vez disso).

> [!NOTE]
> Se lhe for concedido acesso a um ou mais grupos de recursos, em vez de aceder a uma subscrição completa, selecione a subscrição a que esse grupo de recursos pertence. Em seguida, trabalhará no contexto dessa subscrição, mas só poderá aceder aos grupos de recursos designados.

Também pode aceder a funcionalidades relacionadas com subscrições delegadas ou grupos de recursos de dentro de serviços que suportam experiências de gestão de inquilinos cruzados selecionando o grupo de subscrição ou recursos a partir desse serviço.

## <a name="cloud-solution-provider-preview"></a>Provedor de solução de nuvem (pré-visualização)

Uma secção separada do **Cloud Solution Provider (Preview)** da página **My customers** mostra informações e recursos de faturação para os seus clientes CSP que [assinaram o Microsoft Customer Agreement (MCA)](/partner-center/confirm-customer-agreement) e estão [ao abrigo do plano Azure](/partner-center/azure-plan-get-started). Para obter mais informações, consulte [Começar com a sua conta de faturação do Microsoft Partner Agreement](../../cost-management-billing/understand/mpa-overview.md).

Estes clientes CSP aparecerão nesta secção se também os a bordo do Farol de Azure. Da mesma forma, um cliente CSP não tem de aparecer na secção **Cloud Solution Provider (Preview)** dos **meus clientes** para que possa embarcar no Farol de Azure.

## <a name="next-steps"></a>Passos seguintes

- Conheça as [experiências de gestão de inquilinos cruzados.](../concepts/cross-tenant-management-experience.md)
- Saiba como os seus clientes podem [ver e gerir os prestadores de serviços](view-manage-service-providers.md) indo aos **prestadores de serviços** no portal Azure.
