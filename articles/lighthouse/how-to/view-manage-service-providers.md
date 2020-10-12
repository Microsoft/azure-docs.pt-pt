---
title: Ver e gerir fornecedores de serviços
description: Os clientes podem utilizar a página de prestadores de serviços no portal Azure para visualizar informações sobre prestadores de serviços, ofertas de prestadores de serviços e recursos delegados.
ms.date: 08/12/2020
ms.topic: how-to
ms.openlocfilehash: c22408a52d973a244d67528a73d4eaa487f166ba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88167169"
---
# <a name="view-and-manage-service-providers"></a>Ver e gerir fornecedores de serviços

Os clientes podem utilizar a página **de prestadores de serviços** no [portal Azure](https://portal.azure.com) para visualizar detalhes sobre os prestadores de serviços e ofertas de prestadores de serviços, delegar recursos específicos para o [Farol Azure](../overview.md)e fazer compras para novas ofertas de prestadores de serviços.

> [!TIP]
> Enquanto nos referimos a prestadores de serviços e clientes aqui, [as empresas que gerem vários inquilinos](../concepts/enterprise.md) podem usar o mesmo processo para consolidar a sua experiência de gestão.

Para aceder à página **dos prestadores de serviços** no portal Azure, o cliente pode selecionar **Todos os serviços,** em seguida, procurar **por fornecedores de Serviços** e selecioná-lo. Também podem encontrá-lo entrando em "Prestadores de Serviços" ou "Farol Azure" na caixa de pesquisa perto do topo do portal Azure.

> [!NOTE]
> Para visualizar a página dos prestadores de **serviços,** um utilizador no arrendatário do cliente deve ter a [função de Leitor incorporada](../../role-based-access-control/built-in-roles.md#reader) (ou outra função incorporada que inclua o acesso ao Reader).
>
> Para adicionar ou atualizar ofertas, delegar recursos e remover ofertas, o utilizador deve ter o [proprietário papel incorporado](../../role-based-access-control/built-in-roles.md#owner) para a subscrição.

Tenha em mente que a página **dos prestadores de serviços apenas** mostra informações sobre os prestadores de serviços que têm acesso às subscrições ou grupos de recursos do cliente através do Farol de Azure. Se um cliente trabalhar com fornecedores de serviços adicionais que não usam o Farol Azure para aceder aos recursos do cliente, a informação sobre esses prestadores de serviços não é mostrada aqui.

> [!TIP]
> Os prestadores de serviços podem ver informações sobre os seus clientes navegando para **os meus clientes** no portal Azure. Para mais informações, consulte [Ver e gerir clientes e recursos delegados.](view-manage-customers.md)

## <a name="view-service-provider-details"></a>Ver detalhes do prestador de serviços

Para ver detalhes sobre os prestadores de serviços, o cliente pode selecionar ofertas de prestadores de **serviços** no lado esquerdo da página dos prestadores de **serviços.**

Para cada oferta de prestador de serviços, o cliente verá o nome do prestador de serviços e a oferta que lhe está associada, juntamente com o nome que o cliente inseriu durante o processo de embarque.

Na coluna **Delegações,** o cliente vê quantas subscrições e/ou grupos de recursos foram delegados ao prestador de serviços para essa oferta. O prestador de serviços poderá aceder e gerir estas subscrições e/ou grupos de recursos de acordo com os níveis de acesso especificados na oferta.

## <a name="add-or-remove-service-provider-offers"></a>Adicionar ou remover ofertas de prestadores de serviços

Um cliente pode adicionar uma nova oferta de prestador de serviços da página de ofertas do prestador de **serviços,** selecionando **a oferta Add.** O prestador de serviços deve ter publicado uma oferta para este cliente. O cliente pode então selecionar essa oferta a partir do ecrã **de ofertas privadas** e, em seguida, selecionar **Criar**.

Se o cliente quiser remover uma oferta de prestador de serviços, pode selecionar o ícone do caixote do lixo na fila para essa oferta. Após confirmar a supressão, este prestador de serviços deixará de ter acesso aos recursos do cliente que anteriormente estavam delegados para essa oferta.

## <a name="delegate-resources"></a>Delegar recursos

Antes que um prestador de serviços possa aceder e gerir os recursos de um cliente, deve ser delegado. Se um cliente aceitar uma oferta mas ainda não tiver delegado quaisquer recursos, verá uma nota no topo da secção de ofertas de **serviços.** Isto permite ao cliente saber que precisa de tomar medidas antes que o prestador de serviços possa aceder a qualquer um dos recursos do cliente.

Para delegar subscrições ou grupos de recursos:

1. Verifique a caixa para a linha que contém o prestador de serviços, oferta e nome. Em seguida, selecione **recursos delegados** no topo do ecrã.
1. Na secção **de detalhes** da Oferta da página de **recursos delegados,** reveja os detalhes sobre o prestador de serviços e ofereça. Para rever as atribuições de funções para a oferta, **selecione Clique aqui para ver os detalhes da oferta selecionada.**
1. Na secção **Delegado,** selecione **assinaturas delegados** ou **grupos de recursos delegados**.
1. Escolha as subscrições e/ou grupos de recursos que gostaria de delegar para esta oferta e, em seguida, selecione **Add**.
1. Selecione a caixa de verificação na parte inferior da página para confirmar que pretende conceder a este prestador de serviços acesso aos recursos que selecionou e, em seguida, selecione **Delegado**.

## <a name="update-service-provider-offers"></a>Ofertas de prestadores de serviços de atualização

Depois de um cliente ter adicionado uma oferta, um prestador de serviços poderá publicar uma versão atualizada da mesma oferta ao Azure Marketplace. Por exemplo, podem querer adicionar uma nova definição de papel. Se uma nova versão da oferta tiver sido publicada, a página de ofertas do **prestador de serviços** mostrará um ícone de "atualização" na linha para essa oferta. O cliente pode selecionar este ícone para ver as diferenças entre a versão atual da oferta e a nova.

 ![Ícone de oferta de atualização](../media/update-offer.jpg)

Depois de rever as alterações, o cliente pode optar por atualizar para a nova versão. Assim que o fizerem, as autorizações e outras definições especificadas na nova versão aplicar-se-ão a quaisquer subscrições e/ou grupos de recursos que tenham sido delegados para essa oferta.

## <a name="view-delegations"></a>Ver delegações

As delegações representam as atribuições de funções que concedem permissões ao prestador de serviços para os recursos que um cliente delegou. Para ver esta informação, selecione **Delegações** no lado esquerdo da página **de fornecedores** de serviços.

Filtros no topo da página permitem-lhe classificar e agrupar as informações da sua delegação. Também pode filtrar por clientes, ofertas ou palavras-chave específicas.

> [!NOTE]
> Os clientes não verão estas atribuições de funções, ou quaisquer utilizadores do arrendatário prestador de serviços a quem tenha sido concedidas estas funções, ao [visualizar atribuições de funções para o âmbito delegado no portal Azure](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-at-a-scope) ou através de APIs.

## <a name="audit-delegations-in-your-environment"></a>Delegações de auditoria no seu ambiente

Os clientes podem querer ganhar visibilidade nas subscrições e/ou grupos de recursos que foram delegados ao Farol de Azure. Isto é especialmente útil para os clientes com um grande número de subscrições, ou que têm muitos utilizadores que realizam tarefas de gestão.

Fornecemos uma [definição de política incorporada da Azure](../../governance/policy/samples/built-in-policies.md#lighthouse) Para auditar a delegação de âmbitos a um inquilino gestor. Pode atribuir esta política a um grupo de gestão que inclua todas as subscrições que pretende auditar. Quando verificar o cumprimento desta política, quaisquer subscrições e/ou grupos de recursos delegados (dentro do grupo de gestão ao qual a apólice é atribuída) serão apresentados num estado incompatível. Em seguida, pode rever os resultados e confirmar que não existem delegações inesperadas.

Para obter mais informações sobre como atribuir uma política e ver os resultados do estado de conformidade, consulte [Quickstart: Criar uma atribuição de política](../../governance/policy/assign-policy-portal.md).

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [o Farol Azure.](../overview.md)
- Saiba como os prestadores de serviços podem [ver e gerir clientes](view-manage-customers.md) na página **dos meus clientes** no portal Azure.
