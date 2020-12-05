---
title: Atualizar uma delegação
description: Saiba como atualizar uma delegação para um cliente previamente a bordo do Farol de Azure.
ms.date: 12/03/2020
ms.topic: how-to
ms.openlocfilehash: 90827281ac9b05105700298494af0b60b0fa511f
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2020
ms.locfileid: "96610905"
---
# <a name="update-a-delegation"></a>Atualizar uma delegação

Depois de ter acedido a uma subscrição (ou grupo de recursos) para o Farol de Azure, poderá ter de es fazer alterações. Por exemplo, o seu cliente pode querer que você assuma tarefas de gestão adicionais que requerem uma função incorporada Azure diferente, ou você pode precisar de mudar o inquilino ao qual uma subscrição do cliente é delegada.

> [!TIP]
> Embora nos refiramos a prestadores de serviços e clientes neste tópico, [as empresas que gerem vários inquilinos](../concepts/enterprise.md) podem usar o mesmo processo para criar o Farol Azure e consolidar a sua experiência de gestão.

Se [embarcar no seu cliente através dos modelos Azure Resource Manager (ARM),](onboard-customer.md)deve ser realizada uma nova implementação para esse cliente. Dependendo do que está a mudar, pode querer atualizar a oferta original, ou remover a oferta original e criar uma nova.

- **Se estiver a alterar apenas as autorizações:** Pode atualizar a sua delegação alterando apenas a secção de **autorizações** do modelo ARM.
- **Se estiver a mudar o inquilino de gestão:** Tem de criar um novo modelo ARM utilizando um **nome mspOffer** diferente da sua oferta anterior.

## <a name="update-your-arm-template"></a>Atualize o seu modelo ARM

Para atualizar a sua delegação, terá de implementar um modelo ARM que inclua as alterações que pretende fazer.

Se estiver apenas a atualizar autorizações (como adicionar um novo grupo de utilizadores com uma função que não tinha incluído anteriormente, ou alterar o papel para um utilizador existente), pode utilizar o mesmo **nome mspOfferName** que no [modelo ARM](onboard-customer.md#create-an-azure-resource-manager-template) que utilizou para a delegação anterior. Pode utilizar o seu modelo anterior como ponto de partida. Em seguida, faça as alterações necessárias, tais como substituir uma função incorporada do Azure por outra, ou adicionar uma autorização completamente nova ao modelo.

Se alterar o **nome mspOffer,** esta será considerada uma nova oferta separada. Isto é necessário se você estiver mudando o inquilino gerente.

Não é necessário mudar o **nome mspOffer** Se o inquilino gerente permanecer o mesmo. Na maioria dos casos, recomendamos ter apenas um **MSPOfferName** em uso pelo mesmo cliente e gestor de inquilinos. Se optar por alterá-lo de qualquer forma, certifique-se de que a delegação anterior do cliente é removida antes de implementar a nova.

## <a name="remove-the-previous-delegation"></a>Remova a delegação anterior

Antes de realizar uma nova implantação, pode querer [remover o acesso à delegação anterior](remove-delegation.md). Isto garante que todas as permissões anteriores são removidas, permitindo-lhe começar a limpar com os utilizadores/grupos exatos e funções que devem ser aplicadas para a frente.

> [!IMPORTANT]
> Se utilizar um novo **mspOfferName** e mantiver qualquer um dos mesmos valores **principais,** deve remover o acesso à delegação anterior antes de implementar a nova oferta. Se não remover a oferta primeiro, os utilizadores que tenham previamente concedido permissão podem perder o acesso completamente devido a atribuições conflituosas.

Se você está mudando o inquilino gerente, você pode deixar a oferta anterior no lugar se você quiser que ambos os inquilinos continuem a ter acesso. Se você só quer que o novo inquilino gerente tenha acesso, a oferta anterior deve ser removida. Isto pode ser feito antes ou depois de embarcar na nova oferta.

Se estiver a atualizar a oferta para ajustar apenas as autorizações e manter o mesmo **nome mspOffer,** não precisa de remover a delegação anterior. A nova implantação substituirá a delegação anterior, e apenas serão aplicadas as autorizações no modelo mais recente.

:::image type="content" source="../media/update-delegation.jpg" alt-text="Diagrama mostrando quando mudar o mspOfferName e remover uma delegação anterior.":::

A remoção do acesso à delegação pode ser feita por qualquer utilizador do inquilino gerente a quem foi concedido o [Papel de Registo de Registo de Serviços Geridos](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) na delegação original. Se nenhum utilizador no seu inquilino de gestão tiver esta função, pode pedir ao cliente que [remova o acesso à oferta no portal Azure.](view-manage-service-providers.md#add-or-remove-service-provider-offers)

> [!TIP]
> Se retirou a delegação anterior seguindo os passos acima e ainda não conseguiu implementar o novo modelo ARM, poderá ter de [remover completamente a definição de registo](/powershell/module/az.managedservices/remove-azmanagedservicesdefinition). Isto pode ser feito por qualquer utilizador com a função proprietário no inquilino do cliente.  

## <a name="deploy-the-arm-template"></a>Implementar o modelo do Resource Manager

O seu cliente pode [implementar o modelo atualizado](onboard-customer.md#deploy-the-azure-resource-manager-templates) da mesma forma que anteriormente: no portal Azure, utilizando o PowerShell ou utilizando o Azure CLI.

Após a conclusão da implantação, [confirme que foi bem sucedida](onboard-customer.md#confirm-successful-onboarding). As autorizações atualizadas serão então em vigor para o(s) grupo de subscrição ou recursos que o cliente delegou.

## <a name="updating-managed-service-offers"></a>Atualizar ofertas de Serviço Gerido

Se embarcar no seu cliente através de uma oferta de Serviço Gerido publicada no Azure Marketplace, e quiser atualizar as autorizações, pode atualizar a delegação [publicando uma nova versão da sua oferta](../../marketplace/partner-center-portal/update-existing-offer.md) com as autorizações que pretende utilizar [atualizadas](../../marketplace/partner-center-portal/create-new-managed-service-offer.md#authorization) no plano para esse cliente. O cliente poderá então atualizar para a versão mais recente no portal Azure.

Se quiser mudar o inquilino gerente, terá de [criar e publicar uma nova oferta de Serviço Gerido](../../marketplace/partner-center-portal/create-new-managed-service-offer.md) para que o cliente aceite.

> [!TIP]
> Como mencionado anteriormente, recomendamos que não utilize várias ofertas diferentes entre o mesmo cliente e o inquilino gerente. Se publicar uma nova oferta para o mesmo cliente que utiliza o mesmo inquilino gerente, certifique-se de que a oferta anterior é removida antes de o cliente aceitar a oferta mais recente.

## <a name="next-steps"></a>Passos seguintes

- [Ver e gerir clientes](view-manage-customers.md) indo para **os meus clientes** no portal Azure.
- Saiba como [remover o acesso a uma delegação](remove-delegation.md) que estava previamente a bordo.
