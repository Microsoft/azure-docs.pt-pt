---
title: Ver e gerir fornecedores de serviços
description: Os clientes podem utilizar a página de prestadores de serviços no portal Azure para ver informações sobre prestadores de serviços, ofertas de prestadores de serviços e recursos delegados.
ms.date: 04/24/2020
ms.topic: conceptual
ms.openlocfilehash: 7e0522d1cd13ab1a4be589bc0c22c4ebfcf24d3f
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82144319"
---
# <a name="view-and-manage-service-providers"></a>Ver e gerir fornecedores de serviços

Os clientes podem utilizar a página de prestadores de **serviços** no [portal Azure](https://portal.azure.com) para visualizar informações sobre prestadores de serviços e ofertas de prestadores de serviços, delegar recursos específicos através [da gestão de recursos delegados do Azure,](../concepts/azure-delegated-resource-management.md)e fazer compras para novas ofertas de prestadores de serviços. Enquanto nos referimos a prestadores de serviços e clientes aqui, as empresas que gerem vários inquilinos podem usar o mesmo processo para consolidar a sua experiência de gestão.

Para aceder à página de prestadores de **serviços** no portal Azure, o cliente pode selecionar **Todos os serviços,** em seguida, pesquisar por fornecedores de **Serviços** e selecioná-lo. Podem também encontrá-lo entrando em "Prestadores de Serviços" ou "Farol Azure" na caixa de pesquisa perto do topo do portal Azure.

> [!NOTE]
> Para visualizar a página de fornecedores de **serviços,** um utilizador no inquilino do cliente deve ter a [função de Leitor incorporado](../../role-based-access-control/built-in-roles.md#reader) (ou outra função incorporada que inclua o acesso ao Leitor).
>
> Para adicionar ofertas, delegar recursos e remover ofertas, o utilizador deve ter o [papel integrado](../../role-based-access-control/built-in-roles.md#owner) do Proprietário para a subscrição.

Tenha em mente que a página de prestadores de **serviços** apenas mostra informações sobre os prestadores de serviços que têm acesso às subscrições ou grupos de recursos do cliente através da gestão de recursos delegados do Azure. Se um cliente trabalha com fornecedores de serviços adicionais que não usam a gestão de recursos delegados do Azure para aceder aos recursos do cliente, não é aqui apresentada informação sobre esses prestadores de serviços.

> [!TIP]
> Os prestadores de serviços podem ver informações sobre os seus clientes navegando para **os meus clientes** no portal Azure. Para mais informações, consulte [ver e gerir clientes e recursos delegados.](view-manage-customers.md)

## <a name="view-service-provider-details"></a>Ver detalhes do prestador de serviços

Para ver informações sobre prestadores de serviços, o cliente pode selecionar ofertas de **prestador de serviços** no lado esquerdo da página de prestadores de **serviços.**

Para cada oferta do prestador de serviços, o cliente verá o nome do prestador de serviços e a oferta associada ao mesmo, juntamente com o nome que o cliente inseriu durante o processo de embarque.

Na coluna **delegações,** o cliente vê quantas assinaturas e/ou grupos de recursos foram delegados ao prestador de serviços para essa oferta. O prestador de serviços poderá aceder e gerir estas subscrições e/ou grupos de recursos de acordo com os níveis de acesso especificados na oferta.

## <a name="add-or-remove-service-provider-offers"></a>Adicionar ou remover ofertas de prestador de serviços

Um cliente pode adicionar uma nova oferta de prestador de serviços a partir da página oferta do fornecedor de **serviços** selecionando **oferta de Adicionar**. O prestador de serviços deve ter publicado uma oferta para este cliente. O cliente pode então selecionar essa oferta a partir do ecrã **de ofertas Privadas** e, em seguida, selecionar **Criar**.

Se o cliente quiser remover uma oferta de prestador de serviços, pode selecionar o ícone do caixote do lixo na fila para essa oferta. Depois de confirmar a eliminação, esse prestador de serviços deixará de ter acesso aos recursos do cliente que foram anteriormente delegados para essa oferta.

## <a name="delegate-resources"></a>Recursos delegados

Antes que um prestador de serviços possa aceder e gerir os recursos de um cliente, deve ser delegado. Se um cliente aceitou uma oferta mas ainda não delegou quaisquer recursos, verá uma nota no topo da secção de **ofertas** do prestador de serviços. Isto permite ao cliente saber que precisa de agir antes que o prestador de serviços possa aceder a qualquer um dos recursos do cliente.

Para delegar subscrições ou grupos de recursos:

1. Verifique a caixa para a linha que contém o prestador de serviços, a oferta e o nome. Em seguida, selecione **os recursos de delegado** seletivas na parte superior do ecrã.
1. Na secção **de detalhes** da Oferta da página de recursos **do Delegado,** reveja os detalhes sobre o prestador de serviços e a oferta. Para rever as atribuições de funções para a oferta, selecione **Clique aqui para ver os detalhes da oferta selecionada**.
1. Na secção **Delegado,** selecione **subscrições de delegados** ou **grupos de recursos delegados**.
1. Escolha as subscrições e/ou grupos de recursos que deseja delegar para esta oferta e, em seguida, selecione **Adicionar**.
1. Selecione a caixa de verificação na parte inferior da página para confirmar que pretende conceder a este prestador de serviços acesso aos recursos que selecionou e, em seguida, selecione **Delegado**.

## <a name="update-service-provider-offers"></a>Atualizar ofertas de prestador de serviços

Depois de um cliente ter adicionado uma oferta, um prestador de serviços pode publicar uma versão atualizada da mesma oferta ao Azure Marketplace. Por exemplo, podem querer adicionar uma nova definição de papel. Se uma nova versão da oferta tiver sido publicada, o fornecedor de **serviços oferece** uma página mostrará um ícone de "atualização" na linha para essa oferta. O cliente pode selecionar este ícone para ver as diferenças entre a versão atual da oferta e a nova.

 ![Ícone de oferta de atualização](../media/update-offer.jpg)

Depois de rever as alterações, o cliente pode optar por atualizar para a nova versão. Assim que o fizerem, as autorizações e outras definições especificadas na nova versão aplicar-se-ão a quaisquer subscrições e/ou grupos de recursos que tenham sido delegados para essa oferta.

## <a name="view-delegations"></a>Ver delegações

As delegações representam as atribuições de funções que concedem permissões ao prestador de serviços pelos recursos que um cliente delegou. Para ver esta informação, selecione **Delegações** do lado esquerdo da página de prestadores de **serviços.**

Filtros no topo da página permitem-lhe classificar e agrupar as informações da sua delegação. Também pode filtrar por clientes específicos, ofertas ou palavras-chave.

> [!NOTE]
> Os clientes não verão estas atribuições de funções, nem quaisquer utilizadores do inquilino prestador de serviços que tenham recebido estas funções, ao [visualizarem informações](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-at-a-scope) de atribuição de funções para o âmbito delegado no portal Azure ou através de APIs.

## <a name="audit-delegations-in-your-environment"></a>Delegações de auditoria no seu ambiente

Os clientes podem querer ganhar visibilidade nas subscrições e/ou grupos de recursos que tenham sido delegados a prestadores de serviços para [a gestão de recursos delegados do Azure.](../concepts/azure-delegated-resource-management.md) Isto é especialmente útil para aqueles clientes com um grande número de subscrições, ou que têm muitos utilizadores que executam tarefas de gestão.

Fornecemos uma [definição política integrada](../../governance/policy/samples/built-in-policies.md#lighthouse) da Política Azure para auditar a delegação de âmbitos a um inquilino gerente. Pode atribuir esta política a um grupo de gestão que inclui todas as subscrições que pretende auditar. Quando verificar o cumprimento desta política, quaisquer subscrições e/ou grupos de recursos delegados (dentro do grupo de gestão a que a política é atribuída) serão mostrados num estado não conforme. Pode então rever os resultados e confirmar que não existem delegações inesperadas.

Para obter mais informações sobre como atribuir uma política e visualizar os resultados do estado de conformidade, consulte [Quickstart: Create a policy assignment](../../governance/policy/assign-policy-portal.md).

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [o Farol Azure.](../overview.md)
- Saiba como os prestadores de serviços podem [ver e gerir os clientes](view-manage-customers.md) na página dos meus **clientes** no portal Azure.
