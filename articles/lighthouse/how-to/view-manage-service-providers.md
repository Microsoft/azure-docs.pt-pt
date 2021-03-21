---
title: Ver e gerir fornecedores de serviços
description: Os clientes podem utilizar a página de prestadores de serviços no portal Azure para visualizar informações sobre prestadores de serviços, ofertas de prestadores de serviços e recursos delegados.
ms.date: 02/16/2021
ms.topic: how-to
ms.openlocfilehash: f6ee5fb154d75ff715acf99c5184cd1652ccdb80
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100555580"
---
# <a name="view-and-manage-service-providers"></a>Ver e gerir fornecedores de serviços

A página **de prestadores de serviços** no [portal Azure](https://portal.azure.com) dá aos clientes controlo e visibilidade para os seus prestadores de serviços que utilizam [o Farol Azure.](../overview.md) Os clientes podem ver detalhes sobre os prestadores de serviços, delegar recursos específicos, procurar novas ofertas, remover o acesso do prestador de serviços, e muito mais.

Para ver a página **dos prestadores de serviços** no portal Azure, selecione **Todos os serviços,** em seguida, procure **por fornecedores de Serviços** e selecione-o. Também pode encontrar esta página inserindo "Prestadores de Serviços" ou "Farol Azure" na caixa de pesquisa perto do topo do portal Azure.

> [!NOTE]
> Para visualizar a página dos prestadores de **serviços,** um utilizador no arrendatário do cliente deve ter a [função de Leitor incorporada](../../role-based-access-control/built-in-roles.md#reader) (ou outra função incorporada que inclua o acesso ao Reader).
>
> Para adicionar ou atualizar ofertas, delegar recursos e remover ofertas, o utilizador deve ter uma função com a `Microsoft.Authorization/roleAssignments/write` permissão, como [o Proprietário.](../../role-based-access-control/built-in-roles.md#owner)

Tenha em mente que a página **dos prestadores de serviços apenas** mostra informações sobre os prestadores de serviços que têm acesso às subscrições ou grupos de recursos do cliente através do Farol de Azure. Se um cliente trabalhar com fornecedores de serviços adicionais que não usam o Farol Azure, você não verá informações sobre esses prestadores de serviços aqui.

## <a name="view-service-provider-details"></a>Ver detalhes do prestador de serviços

Para ver detalhes sobre os atuais prestadores de serviços que utilizam o Farol Azure para trabalhar no inquilino do cliente, selecione ofertas de prestadores de **serviços** no lado esquerdo da página dos prestadores de **serviços.**

Para cada oferta, verá o nome do prestador de serviços e a oferta associada. Pode selecionar uma oferta para visualizar uma descrição e outros detalhes, incluindo as atribuições de funções que o prestador de serviços foi concedido.

Na coluna **Delegações,** pode ver quantas subscrições e/ou grupos de recursos foram delegados ao prestador de serviços para essa oferta. O prestador de serviços poderá aceder e gerir estas subscrições e/ou grupos de recursos de acordo com os níveis de acesso especificados na oferta.

## <a name="add-or-remove-service-provider-offers"></a>Adicionar ou remover ofertas de prestadores de serviços

Para adicionar uma nova oferta de prestador de serviços na página de ofertas do prestador de **serviços,** selecione **Adicionar oferta.** Selecione **Ofertas Privadas** para visualizar ofertas que um prestador de serviços publicou para este cliente. Pode então selecionar essa oferta a partir do ecrã de **ofertas privadas** e, em seguida, selecione **Configurar + subscrever**.

Pode remover uma oferta de prestador de serviços a qualquer momento, selecionando o ícone do caixote do lixo na fila para essa oferta. Após confirmação da supressão, aquele prestador de serviços deixará de ter acesso aos recursos anteriormente delegados para essa oferta.

## <a name="delegate-resources"></a>Delegar recursos

Antes de um prestador de serviços poder aceder e gerir os recursos de um cliente, uma ou mais subscrições específicas e/ou grupos de recursos devem ser delegados. Se um cliente aceitar uma oferta mas ainda não tiver delegado quaisquer recursos, verá uma nota no topo da secção de ofertas de **serviços.** Isto permite ao cliente saber que precisa de tomar medidas antes que o prestador de serviços possa aceder a qualquer um dos recursos do cliente.

Para delegar subscrições ou grupos de recursos:

1. Verifique a caixa para a linha que contém o prestador de serviços, oferta e nome. Em seguida, selecione **recursos delegados** no topo do ecrã.
1. Na secção **de detalhes** da Oferta da página de **recursos delegados,** reveja os detalhes sobre o prestador de serviços e ofereça. Para rever as atribuições de funções para a oferta, **selecione Clique aqui para ver os detalhes da oferta selecionada.**
1. Na secção **Delegado,** selecione **assinaturas delegados** ou **grupos de recursos delegados**.
1. Escolha as subscrições e/ou grupos de recursos que gostaria de delegar para esta oferta e, em seguida, selecione **Add**.
1. Selecione a caixa de verificação na parte inferior da página para confirmar que pretende conceder a este prestador de serviços acesso aos recursos que selecionou e, em seguida, selecione **Delegado**.

## <a name="update-service-provider-offers"></a>Ofertas de prestadores de serviços de atualização

Depois de um cliente ter adicionado uma oferta, um prestador de serviços poderá publicar uma versão atualizada da mesma oferta ao Azure Marketplace. Por exemplo, podem querer adicionar uma nova definição de papel. Se uma nova versão da oferta tiver sido publicada, a página de ofertas do **prestador de serviços** mostrará um ícone de "atualização" na linha para essa oferta. Selecione este ícone para ver as diferenças entre a versão atual da oferta e a nova.

 ![Ícone de oferta de atualização](../media/update-offer.jpg)

Depois de rever as alterações, o cliente pode optar por atualizar para a nova versão. As autorizações e outras definições especificadas na nova versão aplicar-se-ão então a quaisquer subscrições e/ou grupos de recursos que tenham sido delegados para essa oferta.

## <a name="view-delegations"></a>Ver delegações

As delegações representam as atribuições de funções que concedem permissões ao prestador de serviços para os recursos que um cliente delegou. Para ver esta informação, selecione **Delegações** no lado esquerdo da página **de fornecedores** de serviços.

Filtros no topo da página permitem-lhe classificar e agrupar as informações da sua delegação. Também pode filtrar por clientes, ofertas ou palavras-chave específicas.

> [!NOTE]
> Os clientes não verão estas atribuições de funções, ou quaisquer utilizadores do arrendatário prestador de serviços a quem tenha sido concedidas estas funções, ao [visualizar atribuições de funções para o âmbito delegado no portal Azure](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-at-a-scope) ou através de APIs.

## <a name="audit-delegations-in-your-environment"></a>Delegações de auditoria no seu ambiente

Os clientes podem querer ganhar visibilidade nas subscrições e/ou grupos de recursos que foram delegados ao Farol de Azure. Isto é especialmente útil para os clientes com um grande número de subscrições, ou que têm muitos utilizadores que realizam tarefas de gestão.

Fornecemos uma [definição de política integrada da Azure](../../governance/policy/samples/built-in-policies.md#lighthouse) Para auditar a [delegação de âmbitos a um inquilino gestor.](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/Lighthouse_Delegations_Audit.json) Pode atribuir esta política a um grupo de gestão que inclua todas as subscrições que pretende auditar. Quando verificar o cumprimento desta política, quaisquer subscrições e/ou grupos de recursos delegados (dentro do grupo de gestão ao qual a apólice é atribuída) serão apresentados num estado incompatível. Em seguida, pode rever os resultados e confirmar que não existem delegações inesperadas.

Outra [definição de política incorporada](../../governance/policy/samples/built-in-policies.md#lighthouse) permite restringir as [delegações a inquilinos de gestão específica.](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/AllowCertainManagingTenantIds_Deny.json) Esta política pode igualmente ser aplicada a um grupo de gestão que inclua quaisquer subscrições para as quais pretende limitar as delegações. Após a implementação da apólice, qualquer tentativa de delegar uma assinatura a um inquilino fora dos que especificar será negada.

Para obter mais informações sobre como atribuir uma política e ver os resultados do estado de conformidade, consulte [Quickstart: Criar uma atribuição de política](../../governance/policy/assign-policy-portal.md).

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [o Farol Azure.](../overview.md)
- Saiba como auditar a [atividade do prestador de serviços.](view-service-provider-activity.md)
- Saiba como os prestadores de serviços podem [ver e gerir clientes](view-manage-customers.md) na página **dos meus clientes** no portal Azure.
- Saiba como [as empresas que gerem vários inquilinos](../concepts/enterprise.md) podem usar o Farol Azure para consolidar a sua experiência de gestão.

