---
title: Ofertas de serviços geridos no Azure Marketplace
description: As ofertas de serviços geridos permitem aos prestadores de serviços vender ofertas de gestão de recursos aos clientes do Azure Marketplace.
ms.date: 03/17/2020
ms.topic: conceptual
ms.openlocfilehash: 2d6e39f753736c0582e9d91870a99b66ae41255b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500779"
---
# <a name="managed-services-offers-in-azure-marketplace"></a>Ofertas de serviços geridos no Azure Marketplace

Este artigo descreve o novo tipo de oferta de **Serviços Geridos** no [Mercado Azure.](https://azuremarketplace.microsoft.com) As ofertas de serviços geridos permitem-lhe oferecer serviços de gestão de recursos a clientes com gestão de recursos delegados do Azure. Pode disponibilizar estas ofertas a todos os potenciais clientes ou apenas a um ou mais clientes específicos. Uma vez que fatura os clientes diretamente pelos custos relacionados com estes serviços geridos, não existem taxas cobradas pela Microsoft.

## <a name="understand-managed-services-offers"></a>Compreender ofertas de serviços geridos

Os serviços geridos oferecem agilizar o processo de entrada de clientes para a gestão de recursos delegados do Azure. Quando um cliente comprar uma oferta no Azure Marketplace, poderá especificar quais as subscrições e/ou grupos de recursos que devem ser a bordo. Note que a subscrição deve ser autorizada primeiro para o embarque através do registo manual do fornecedor de recursos **Microsoft.ManagedServices.**

Depois disso, os utilizadores da sua organização poderão realizar tarefas de administração para esses recursos a partir do arrendatário da sua organização, de acordo com o acesso que definiu ao criar a oferta no Portal do [Parceiro cloud.](https://cloudpartner.azure.com/) Isto é feito através de um manifesto que especifica os utilizadores, grupos e diretores de serviço seletivas do Azure que terão acesso aos recursos dos clientes utilizando a gestão de recursos delegados do Azure, juntamente com funções que definem o seu nível de acesso. Ao atribuir permissões a um grupo DeD Azure em vez de uma série de contas individuais de utilizador ou aplicação, pode adicionar ou remover utilizadores individuais quando os seus requisitos de acesso mudarem.

## <a name="public-and-private-offers"></a>Ofertas públicas e privadas

Cada oferta de serviços geridos inclui um ou mais planos. Os planos podem ser privados ou públicos. 

Se quiser limitar a sua oferta a clientes específicos, pode publicar um plano privado. Quando o fizer, o plano só pode ser adquirido para as iDs de subscrição específicas que fornece. Para mais informações, consulte [ofertas privadas.](../../marketplace/private-offers.md)

Os planos públicos permitem-lhe promover os seus serviços a novos clientes. Estes são geralmente mais apropriados quando você só requer acesso limitado ao inquilino do cliente. Uma vez estabelecido uma relação com um cliente, se decidir conceder à sua organização acesso adicional, pode fazê-lo publicando um novo plano privado apenas para esse cliente, ou [inserindo-os para mais acesso usando modelos do Gestor de Recursos Azure.](../how-to/onboard-customer.md)

Se for caso disso, pode incluir planos públicos e privados na mesma oferta.

> [!IMPORTANT]
> Uma vez que um plano é publicado como público, não pode mudá-lo para privado. Para controlar quais os clientes que podem aceitar a sua oferta e delegar recursos, utilize um plano privado. Com um plano público, não é possível restringir a disponibilidade a determinados clientes ou mesmo a um certo número de clientes (embora possa deixar de vender o plano completamente se optar por fazê-lo). Só pode [remover o acesso a uma delegação](../how-to/onboard-customer.md#remove-access-to-a-delegation) depois de um cliente aceitar uma oferta apenas se incluir uma **Autorização** com a Definição de **Funções** definida para a Atribuição de Inscrição de [Serviços Geridos Eliminar funções](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) quando publicou a oferta. Também pode contactar o cliente e pedir-lhe que [remova o seu acesso](../how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers).

## <a name="publish-managed-service-offers"></a>Publicar ofertas de serviçogeridos

Para aprender a publicar uma oferta de serviços geridos, consulte [A Publish a Managed Services offer to Azure Marketplace](../how-to/publish-managed-services-offers.md). Para informações gerais sobre a publicação no Azure Marketplace utilizando o Portal do Parceiro cloud, consulte o [Azure Marketplace e o AppSource Publishing Guide](../../marketplace/marketplace-publishers-guide.md) e gere o Azure e o [AppSource Marketplace.](../../marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers.md)

## <a name="next-steps"></a>Passos seguintes

- Conheça as experiências [de gestão de recursos delegados](azure-delegated-resource-management.md) da Azure e [de gestão de inquilinos transversais.](cross-tenant-management-experience.md)
- [Publique ofertas de serviços geridos](../how-to/publish-managed-services-offers.md) ao Azure Marketplace.
