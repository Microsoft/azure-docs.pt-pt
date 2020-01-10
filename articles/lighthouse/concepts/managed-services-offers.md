---
title: Ofertas de serviços geridos no Azure Marketplace
description: Os serviços gerenciados oferecem aos provedores de serviços para vender ofertas de gerenciamento de recursos para clientes no Azure Marketplace.
ms.date: 12/16/2019
ms.topic: conceptual
ms.openlocfilehash: 1b4f0d7457a74afe710a48f429cfe47535a9b122
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453588"
---
# <a name="managed-services-offers-in-azure-marketplace"></a>Ofertas de serviços geridos no Azure Marketplace

Este artigo descreve o novo tipo de oferta de **Serviços gerenciados** no [Azure Marketplace](https://azuremarketplace.microsoft.com). As ofertas de serviços gerenciados permitem que você ofereça serviços de gerenciamento de recursos aos clientes com o gerenciamento de recursos delegado do Azure. Você pode tornar essas ofertas disponíveis para todos os clientes potenciais ou apenas para um ou mais clientes específicos. Como você cobra os clientes diretamente por custos relacionados a esses serviços gerenciados, não há tarifas cobradas pela Microsoft.

## <a name="understand-managed-services-offers"></a>Entenda as ofertas de serviços gerenciados

As ofertas de serviços gerenciados simplificam o processo de integração de clientes para o gerenciamento de recursos delegado do Azure. Quando um cliente adquire uma oferta no Azure Marketplace, ele poderá especificar quais assinaturas e/ou grupos de recursos devem ser integrados. Observe que a assinatura deve primeiro ser autorizada para integração Registrando manualmente o provedor de recursos **Microsoft. managedservices** .

Depois disso, os usuários em sua organização poderão executar tarefas de administração para esses recursos de dentro do locatário da sua organização, de acordo com o acesso que você definiu ao criar a oferta no [portal do Cloud Partner](https://cloudpartner.azure.com/). Isso é feito por meio de um manifesto que especifica os usuários, grupos e entidades de serviço do Azure AD que terão acesso aos recursos do cliente usando o gerenciamento de recursos delegado do Azure, juntamente com funções que definem seu nível de acesso. Ao atribuir permissões a um grupo do Azure AD em vez de uma série de contas individuais de usuário ou aplicativo, você pode adicionar ou remover usuários individuais quando seus requisitos de acesso mudam.

## <a name="public-and-private-offers"></a>Ofertas públicas e privadas

Cada oferta de serviços gerenciados inclui um ou mais planos. Os planos podem ser privados ou públicos. 

Se você quiser limitar sua oferta a clientes específicos, poderá publicar um plano privado. Quando você fizer isso, o plano só poderá ser adquirido para as IDs de assinatura específicas que você fornecer. Para obter mais informações, consulte [ofertas privadas](../../marketplace/private-offers.md).

Os planos públicos permitem promover seus serviços a novos clientes. Normalmente, eles são mais apropriados quando você só precisa de acesso limitado ao locatário do cliente. Depois de estabelecer uma relação com um cliente, se decidir conceder acesso adicional à sua organização, você poderá fazer isso publicando um novo plano privado somente para esse cliente ou inserindo [-os para obter acesso adicional usando modelos de Azure Resource Manager](../how-to/onboard-customer.md).

Se apropriado, você pode incluir planos públicos e privados na mesma oferta.

> [!IMPORTANT]
> Depois que um plano tiver sido publicado como público, você não poderá alterá-lo para privado. Para controlar quais clientes podem aceitar sua oferta e delegar recursos, use um plano privado. Com um plano público, não é possível restringir a disponibilidade para determinados clientes ou até mesmo para um determinado número de clientes (embora você possa deixar de vender completamente o plano se optar por fazê-lo). Atualmente, não há nenhum mecanismo para rejeitar ou remover delegações depois que um cliente aceita uma oferta, embora você sempre possa entrar em contato com um cliente e pedir que ele [remova seu acesso](../how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers).

## <a name="publish-managed-service-offers"></a>Publicar ofertas de serviço gerenciado

Para saber como publicar uma oferta de serviços gerenciados, consulte [publicar uma oferta de serviços gerenciados no Azure Marketplace](../how-to/publish-managed-services-offers.md). Para obter informações gerais sobre como publicar no Azure Marketplace usando o Portal do Cloud Partner, consulte [Guia de publicação do Azure Marketplace e AppSource](../../marketplace/marketplace-publishers-guide.md) e [gerenciar ofertas do Azure e do AppSource Marketplace](../../marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers.md).

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [Gerenciamento de recursos delegado do Azure](azure-delegated-resource-management.md) e [experiências de gerenciamento entre locatários](cross-tenant-management-experience.md).
- [Publicar ofertas de serviços gerenciados](../how-to/publish-managed-services-offers.md) no Azure Marketplace.
