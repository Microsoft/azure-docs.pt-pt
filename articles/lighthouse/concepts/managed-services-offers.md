---
title: Gerido ofertas de serviços no Azure Marketplace
description: Ofertas de permitir que os fornecedores de serviços vender ofertas de gestão de recursos para os clientes no Azure Marketplace de serviços geridos.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: a6fcf5f1d0ac194d60f834fb8d26db019c538410
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809877"
---
# <a name="managed-services-offers-in-azure-marketplace"></a>Gerido ofertas de serviços no Azure Marketplace

Este artigo descreve a nova **serviços geridos pelo** oferecem escreva [Azure Marketplace](https://azuremarketplace.microsoft.com). Ofertas permitem-lhe oferecer serviços de gestão de recursos para os clientes com a gestão de recursos do Azure de delegado de serviços geridos. Pode fazer estas ofertas disponíveis para todos os potenciais clientes ou apenas para um ou mais clientes específicos. Uma vez que a cobrar aos clientes diretamente para os custos relacionados a esses serviços geridos, não existem sem taxas cobradas pela Microsoft.

## <a name="understand-managed-services-offers"></a>Compreender as ofertas de serviços geridos

Ofertas de serviços geridos simplificam o processo de inclusão de clientes para gestão de recursos de delegada do Azure. Assim que um cliente compra uma oferta no Azure Marketplace, poderá especificar que as assinaturas e/ou grupos de recursos devem ser incluído para que o especificado a utilizadores na sua organização podem realizar tarefas de administração do cliente a partir da sua inquilino da organização.

Depois disso, é necessária nenhuma ação adicional pelo cliente ou o fornecedor de serviços para subscrever o cliente. Isto acontece porque quando define a oferta no [Cloud Partner Portal](https://cloudpartner.azure.com/), criar um manifesto que especifica os utilizadores do Azure AD, grupos e recursos de delegado de princípios de serviço que terão acesso a recursos de cliente através do Azure gestão. juntamente com as funções que definem o nível de acesso. Ao atribuir permissões a um grupo do Azure AD em vez de uma série de usuários ou contas de aplicação, pode adicionar ou remover utilizadores individuais quando os seus requisitos de acesso são alterados.

## <a name="public-and-private-offers"></a>Ofertas privadas e públicas

Cada oferta de serviços geridos inclui um ou mais planos. Estes planos podem ser privado ou público.

Se pretender limitar a sua oferta aos clientes específicos, pode publicar um plano privado. Ao fazê-lo, só pode comprar o plano para as específicas] IDs que fornece de subscrição. Para mais informações, veja [ofertas privadas](https://docs.microsoft.com/azure/marketplace/private-offers).

Planos públicos permitem-lhe promover seus serviços para novos clientes. Estes são normalmente mais apropriadas quando necessitar apenas de acesso limitado ao inquilino do cliente. Assim que tiver estabelecido uma relação com um cliente, se decidirem conceder o acesso adicional da organização, pode fazê-lo ao publicar um novo plano privado para que apenas o cliente ou por [inclusão-los para ainda mais o acesso através do Azure Modelos do Resource Manager](../how-to/onboard-customer.md).

Tenha em atenção que depois de um plano foi publicado como públicas, não é possível de alterá-la como privadas. Além disso, não é possível restringir a disponibilidade de um plano pública para determinados clientes ou mesmo para um determinado número de clientes, embora pode parar a vender completamente o plano, se optar por fazê-lo.

Se tal for apropriado, pode incluir planos públicos e privados na oferta do mesmo.

## <a name="publish-managed-service-offers"></a>Publicar ofertas de serviço gerido

Para saber como publicar uma oferta de serviços geridos, veja [publicar uma oferta de serviços geridos pelo Azure Marketplace](../how-to/publish-managed-services-offers.md). Para obter informações gerais sobre a publicação no Azure Marketplace através do Portal de parceiro de Cloud, veja [Azure Marketplace e guia de publicação do AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide) e [ofertas de gerir o Azure e o Marketplace do AppSource](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers).

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [Azure delegadas a gestão de recursos](azure-delegated-resource-management.md) e [entre inquilinos experiências de gestão](cross-tenant-management-experience.md).
- [Publicar ofertas de serviços geridos](../how-to/publish-managed-services-offers.md) no Azure Marketplace.
