---
title: Gestão de recursos delegados do Azure
description: Os serviços gerenciados oferecem aos provedores de serviços para vender ofertas de gerenciamento de recursos para clientes no Azure Marketplace.
ms.date: 07/11/2019
ms.topic: conceptual
ms.openlocfilehash: 2baa66acb4abc250ff69a1560f1bc6fe9a531ba8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453633"
---
# <a name="azure-delegated-resource-management"></a>Gestão de recursos delegados do Azure

O gerenciamento de recursos delegado do Azure é um dos principais componentes do Azure Lighthouse. Com o gerenciamento de recursos delegado do Azure, os provedores de serviço podem simplificar as experiências de envolvimento e integração do cliente, Gerenciando recursos delegados em escala com agilidade e precisão.

## <a name="what-is-azure-delegated-resource-management"></a>O que é o gerenciamento de recursos delegado do Azure?

O gerenciamento de recursos delegado do Azure permite a projeção lógica de recursos de um locatário para outro locatário. Isso permite que usuários autorizados em um locatário Azure Active Directory (Azure AD) realizem operações de gerenciamento em diferentes locatários do Azure AD que pertencem a seus clientes. Os provedores de serviços podem entrar em seu próprio locatário do AD do Azure e ter autorização para trabalhar em grupos de recursos e assinaturas de clientes delegados. Isso permite que eles realizem operações de gerenciamento em nome de seus clientes, sem precisar entrar em cada locatário individual do cliente.

> [!NOTE]
> O gerenciamento de recursos delegado do Azure também pode ser usado [em uma empresa que tem vários locatários do Azure ad próprios](enterprise.md) para simplificar o gerenciamento entre locatários.

Com o gerenciamento de recursos delegado do Azure, os usuários autorizados podem trabalhar diretamente no contexto de uma assinatura de cliente sem ter uma conta no locatário do cliente ou ser um coproprietário do locatário do cliente. Eles também podem [Exibir e gerenciar todas as assinaturas de clientes delegadas na página novos **meus clientes** ](../how-to/view-manage-customers.md) no portal do Azure.

A [experiência de gerenciamento entre locatários](cross-tenant-management-experience.md) ajuda você a trabalhar de maneira mais eficiente com os serviços de gerenciamento do Azure, como Azure Policy, a central de segurança do Azure e muito mais. Todas as atividades do provedor de serviços são controladas no log de atividades, que é armazenado nos locatários do provedor de serviços e do cliente. Isso significa que o cliente e o provedor de serviços podem identificar facilmente o usuário associado a quaisquer alterações.

Quando você integrar um cliente ao gerenciamento de recursos delegado do Azure, ele terá acesso à nova página **provedores de serviços** na portal do Azure, onde poderá [confirmar e gerenciar suas ofertas, provedores de serviços e recursos delegados](../how-to/view-manage-service-providers.md). Se o cliente quiser revogar o acesso para um provedor de serviços, ele poderá fazer isso aqui a qualquer momento.

Você pode [publicar o novo tipo de oferta de serviços gerenciados no Azure Marketplace](../how-to/publish-managed-services-offers.md) para integrar facilmente os clientes ao gerenciamento de recursos delegado do Azure. Como alternativa, você pode [concluir o processo de integração implantando modelos de Azure Resource Manager](../how-to/onboard-customer.md).

## <a name="how-azure-delegated-resource-management-works"></a>Como funciona o gerenciamento de recursos delegado do Azure

Em um alto nível, veja como funciona o gerenciamento de recursos delegado do Azure:

1. Como um provedor de serviços, você identifica o acesso (funções) que seus grupos, entidades de serviço ou usuários precisarão gerenciar os recursos do Azure do cliente. A definição de acesso contém a ID de locatário do provedor de serviços junto com o acesso necessário para a oferta, definida usando as identidades de **PrincipalId** do seu locatário mapeado para [valores **roleDefinition** internos](../../role-based-access-control/built-in-roles.md) (colaborador, colaborador de VM, leitor, etc.).
2. Você especifica esse acesso e integra o cliente ao gerenciamento de recursos delegado do Azure de uma das duas maneiras:
   - [Publicar uma oferta de serviços gerenciados do Azure Marketplace](../how-to/publish-managed-services-offers.md) (privada ou pública) que o cliente aceitará
   - [Implantar um modelo de Azure Resource Manager no locatário do cliente](../how-to/onboard-customer.md) para uma ou mais assinaturas ou grupos de recursos específicos
3. Depois que o cliente tiver sido integrado, os usuários autorizados poderão entrar no seu locatário do provedor de serviços e executar tarefas de gerenciamento no escopo determinado do cliente, com base no acesso que você definiu.

## <a name="support-for-azure-delegated-resource-management"></a>Suporte para gerenciamento de recursos delegados do Azure

Se precisar de ajuda relacionada ao gerenciamento de recursos delegado do Azure, você poderá abrir uma solicitação de suporte no portal do Azure. Para **tipo de problema**, escolha **técnico**. Selecione uma assinatura e, em seguida, selecione **Gerenciamento de recursos delegado** (em **monitoramento & gerenciamento**).

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre as [experiências de gerenciamento entre locatários](cross-tenant-management-experience.md).
- Saiba mais sobre as [ofertas de serviços gerenciados no Azure Marketplace](managed-services-offers.md).