---
title: Ofertas de Serviço Gerido no Azure Marketplace
description: As ofertas de Serviço Gerido permitem-lhe vender ofertas de gestão de recursos a clientes no Azure Marketplace.
ms.date: 07/28/2020
ms.topic: conceptual
ms.openlocfilehash: 6c3047cd95128f689e75d9c1f5fba5a39f86291c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88163327"
---
# <a name="managed-service-offers-in-azure-marketplace"></a>Ofertas de Serviço Gerido no Azure Marketplace

Este artigo descreve o tipo de oferta **de serviço gerido** no [Azure Marketplace.](https://azuremarketplace.microsoft.com) As ofertas de Serviço Gerido permitem-lhe oferecer serviços de gestão de recursos aos clientes através [do Farol Azure.](../overview.md) Pode disponibilizar estas ofertas a todos os potenciais clientes, ou apenas a um ou mais clientes específicos. Uma vez que cobra diretamente aos clientes os custos relacionados com estes serviços geridos, não existem taxas cobradas pela Microsoft.

## <a name="understand-managed-service-offers"></a>Compreender ofertas de Serviço Gerido

O Managed Service oferece agilização do processo de embarque de clientes para o Farol de Azure. Quando um cliente comprar uma oferta no Azure Marketplace, poderá especificar quais as subscrições e/ou grupos de recursos que devem ser a bordo.

Depois disso, os utilizadores da sua organização poderão trabalhar nesses recursos a partir do seu inquilino gerente através da [gestão de recursos delegados da Azure,](azure-delegated-resource-management.md)de acordo com o acesso que definiu ao criar a oferta. Isto é feito através de um manifesto que especifica os utilizadores, grupos e diretores de serviço Azure Ative Que terão acesso aos recursos dos clientes, juntamente com funções que definem o seu nível de acesso. Ao atribuir permissões a um grupo Azure AD em vez de uma série de contas individuais de utilizador ou aplicação, pode adicionar ou remover utilizadores individuais quando os seus requisitos de acesso mudarem.

## <a name="public-and-private-offers"></a>Ofertas públicas e privadas

Cada oferta de Serviços Geridos inclui um ou mais planos. Os planos podem ser privados ou públicos.

Se quiser limitar a sua oferta a clientes específicos, pode publicar um plano privado. Quando o fizer, o plano só pode ser adquirido para os IDs de subscrição específicos que fornece. Para mais informações, consulte [ofertas privadas.](../../marketplace/private-offers.md)

> [!NOTE]
> As ofertas privadas não são suportadas com subscrições estabelecidas através de um revendedor do programa Cloud Solution Provider (CSP).

Os planos públicos permitem promover os seus serviços a novos clientes. Estes são geralmente mais adequados quando você só precisa de acesso limitado ao inquilino do cliente. Uma vez estabelecida uma relação com um cliente, se decidirem conceder à sua organização acesso adicional, pode fazê-lo publicando um novo plano privado apenas para esse cliente, quer [a bordo deles para mais acesso utilizando modelos do Azure Resource Manager.](../how-to/onboard-customer.md)

Se for caso disso, pode incluir planos públicos e privados na mesma oferta.

> [!IMPORTANT]
> Uma vez que um plano tenha sido publicado como público, não se pode mudá-lo para privado. Para controlar quais os clientes que podem aceitar a sua oferta e delegar recursos, utilize um plano privado. Com um plano público, não é possível restringir a disponibilidade a determinados clientes ou mesmo a um determinado número de clientes (embora possa parar de vender completamente o plano se optar por fazê-lo). Pode [remover o acesso a uma delegação](../how-to/remove-delegation.md) depois de um cliente aceitar uma oferta apenas se tiver incluído uma **Autorização** com a **Definição de Função** definida para [Função de Atribuição de Registo de Serviços Geridos](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) Quando publicou a oferta. Também pode contactar o cliente e pedir-lhe que [remova o seu acesso.](../how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers)

## <a name="publish-managed-service-offers"></a>Publicar ofertas de Serviço Gerido

Para aprender a publicar uma oferta de Serviços Geridos, consulte [publicar uma oferta de Serviços Geridos ao Azure Marketplace.](../how-to/publish-managed-services-offers.md)

## <a name="next-steps"></a>Passos seguintes

- Conheça as [experiências de gestão de recursos delegadas da Azure](azure-delegated-resource-management.md) e [da gestão de inquilinos.](cross-tenant-management-experience.md)
- [Publicar Serviços Geridos oferece](../how-to/publish-managed-services-offers.md) ao Azure Marketplace.
