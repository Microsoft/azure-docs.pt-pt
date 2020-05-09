---
title: Azure Lighthouse e aplicações geridas do Azure
description: Azure Lighthouse e Azure geriram aplicações...
ms.date: 05/01/2020
ms.topic: conceptual
ms.openlocfilehash: d9923d31e78675927b4ca235607b2a61b24ccc41
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82783633"
---
# <a name="azure-lighthouse-and-azure-managed-applications"></a>Azure Lighthouse e aplicações geridas do Azure

Tanto o Azure geriu aplicações como o trabalho do Farol Azure, permitindo a um prestador de serviços aceder aos recursos que residem no inquilino do cliente. Pode ser útil compreender as diferenças na forma como funcionam e nos cenários que ajudam a permitir, bem como como podem ser usados em conjunto.

## <a name="comparing-azure-lighthouse-and-azure-managed-applications"></a>Comparar as aplicações geridas pelo Farol Azure e azure

### <a name="azure-lighthouse"></a>Azure Lighthouse

Com o [Azure Lighthouse](../overview.md), um prestador de serviços para executar uma vasta gama de tarefas de gestão diretamente na subscrição de um cliente (ou grupo de recursos). Este acesso é obtido através de uma projeção lógica, permitindo que os prestadores de serviços inscrevam-se no seu próprio inquilino e acedam a recursos que pertencem ao inquilino do cliente. O cliente pode determinar quais as subscrições ou grupos de recursos para delegar no prestador de serviços, e o cliente mantém acesso total a esses recursos. Também podem remover o acesso do prestador de serviços a qualquer momento.

Para utilizar o Farol Azure, os clientes estão a bordo para [a gestão de recursos delegados do Azure,](azure-delegated-resource-management.md) quer através da implementação de [modelos ARM,](../how-to/onboard-customer.md) quer através de uma oferta de [Serviço Gerido no Azure Marketplace.](managed-services-offers.md) Pode acompanhar o seu impacto nos compromissos do cliente [ligando o ID do seu parceiro.](../../cost-management-billing/manage/link-partner-id.md)

O Farol Azure é normalmente utilizado quando um prestador de serviços irá executar tarefas de gestão para um cliente de forma contínua.

### <a name="azure-managed-applications"></a>Aplicações geridas do Azure

As [aplicações geridas pela Azure](../../azure-resource-manager/managed-applications/overview.md) permitem a um prestador de serviços ou ao ISV oferecer soluções cloud que são fáceis de implementar e usar nas suas próprias subscrições.

Numa aplicação gerida, os recursos utilizados pela aplicação são agregados e implantados num grupo de recursos gerido pela editora. Este grupo de recursos está presente na subscrição do cliente, mas uma identidade no inquilino da editora tem acesso ao mesmo. O ISV continua a gerir e a manter a aplicação gerida, enquanto o cliente não tem acesso direto ao trabalho no seu grupo de recursos, nem qualquer acesso aos seus recursos.

As aplicações geridas suportam experiências personalizadas do [portal Azure](../../azure-resource-manager/managed-applications/concepts-view-definition.md) e [integração com fornecedores personalizados.](../../azure-resource-manager/managed-applications/tutorial-create-managed-app-with-custom-provider.md) Estas opções podem ser usadas para proporcionar uma experiência mais personalizada e integrada, facilitando aos clientes a realização de algumas tarefas de gestão.

As aplicações geridas podem ser [publicadas no Azure Marketplace,](../../azure-resource-manager/managed-applications/publish-marketplace-app.md)seja como uma oferta privada para uso específico de um cliente, ou como ofertas públicas que vários clientes podem comprar. Também podem ser entregues aos utilizadores dentro da sua organização através da publicação de [aplicações geridas no seu catálogo de serviços.](../../azure-resource-manager/managed-applications/publish-service-catalog-app.md) Você pode implementar tanto o catálogo de serviços como as instâncias do Marketplace usando modelos ARM, que podem incluir um identificador exclusivo de um parceiro de Mercado Comercial para rastrear a atribuição de uso do [cliente](../../marketplace/azure-partner-customer-usage-attribution.md).

As aplicações geridas pelo Azure são normalmente utilizadas para uma necessidade específica do cliente que pode ser conseguida através de uma solução chave-na-mão que é totalmente gerida pelo prestador de serviços.

## <a name="using-azure-lighthouse-and-azure-managed-applications-together"></a>Utilizando o Farol Azure e o Azure geriam aplicações em conjunto

Enquanto as aplicações geridas pelo Azure Lighthouse e azure utilizam diferentes mecanismos de acesso para atingir objetivos diferentes, pode haver cenários em que faça sentido que um prestador de serviços utilize ambas com o mesmo cliente.

Por exemplo, um cliente pode querer serviços geridos por um prestador de serviços através do Farol Azure, de modo a que tenha visibilidade nas ações do parceiro, juntamente com o controlo contínuo da sua subscrição delegada. No entanto, o prestador de serviços pode não querer que o cliente aceda a determinados recursos que serão armazenados no inquilino do cliente, ou permitir quaisquer ações personalizadas sobre esses recursos. Para cumprir estes objetivos, o prestador de serviços pode publicar uma oferta privada como uma aplicação gerida. A aplicação gerida pode incluir um grupo de recursos que é implantado no inquilino do cliente, mas que não pode ser acedido diretamente pelo cliente.

Os clientes também podem estar interessados em aplicações geridas de vários prestadores de serviços, quer também utilizem ou não serviços geridos através do Farol Azure de qualquer um desses prestadores de serviços. Além disso, os parceiros do programa Cloud Solution Provider (CSP) podem revender certas aplicações geridas publicadas por outros ISVs a clientes que suportam através do Farol Azure. Com um vasto leque de opções, os prestadores de serviços podem escolher o equilíbrio certo para satisfazer as necessidades dos seus clientes, limitando ao mesmo tempo o acesso aos recursos quando apropriado.

## <a name="next-steps"></a>Passos seguintes

- Conheça as [aplicações geridas pelo Azure.](../../azure-resource-manager/managed-applications/overview.md)
- Saiba como [embarcar numa subscrição da gestão de recursos delegados do Azure.](../how-to/onboard-customer.md)
