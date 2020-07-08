---
title: Publicar uma oferta de Serviço Gerido para o Azure Marketplace
description: Saiba como publicar uma oferta de Serviço Gerido que a bordo dos clientes para a Azure delegada gestão de recursos.
ms.date: 05/04/2020
ms.topic: how-to
ms.openlocfilehash: 214a71faca59072660f1e1f413cb107d8e8f6fc9
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85920900"
---
# <a name="publish-a-managed-service-offer-to-azure-marketplace"></a>Publicar uma oferta de Serviço Gerido para o Azure Marketplace

Neste artigo, você aprenderá a publicar uma oferta de Serviço Gerido público ou privado ao [Azure Marketplace](https://azuremarketplace.microsoft.com) usando o programa [De Marketplace Comercial](../../marketplace/partner-center-portal/commercial-marketplace-overview.md) no Partner Center. Os clientes que adquirem a oferta podem então embarcar subscrições e grupos de recursos para [a gestão de recursos delegados da Azure.](../concepts/azure-delegated-resource-management.md)

## <a name="publishing-requirements"></a>Requisitos de publicação

Precisa de ter uma conta válida [no Partner Center](../../marketplace/partner-center-portal/create-account.md) para criar e publicar ofertas. Se ainda não tiver uma conta, o processo de inscrição irá [levá-lo](https://aka.ms/joinmarketplace) através dos passos de criação de uma conta no Partner Center e inscrição no programa Mercado Comercial.

De acordo com os [requisitos de certificação do Serviço Gerido,](/legal/marketplace/certification-policies#7004-business-requirements)você deve ter um [nível](/partner-center/learn-about-competencies) de competência silver ou Gold Cloud Platform ou ser um [Azure Expert MSP](https://partner.microsoft.com/membership/azure-expert-msp) para publicar uma oferta de Serviço Gerido.

O seu ID da Microsoft Partner Network (MPN) será [automaticamente associado às](../../billing/billing-partner-admin-link-started.md) ofertas que publicar para acompanhar o seu impacto através dos compromissos com os clientes.

> [!NOTE]
> Se não quiser publicar uma oferta ao Azure Marketplace, pode embarcar manualmente os clientes utilizando modelos do Azure Resource Manager. Para obter mais informações, consulte [a bordo de um cliente da Azure delegada na gestão de recursos.](onboard-customer.md)

## <a name="create-your-offer"></a>Crie a sua oferta

Para instruções detalhadas sobre como criar a sua oferta, incluindo todas as informações e bens que necessitará de fornecer, consulte [Create a Managed Service offer](../../marketplace/partner-center-portal/create-new-managed-service-offer.md).

Para saber mais sobre o processo de publicação geral, consulte [o Azure Marketplace e o AppSource Publishing Guide](../../marketplace/marketplace-publishers-guide.md). Deve também rever as [políticas de certificação](/legal/marketplace/certification-policies)do mercado comercial, nomeadamente a secção [serviços geridos.](/legal/marketplace/certification-policies#700-managed-services)

Uma vez que um cliente adicione a sua oferta, eles serão capazes de delegar uma ou mais subscrições ou grupos de recursos, que serão então [a bordo para a gestão de recursos delegados Azure.](#the-customer-onboarding-process)

> [!IMPORTANT]
> Cada plano de uma oferta de Serviço Gerido inclui uma secção **De Detalhes Manifesto,** onde define as entidades do Azure Ative Directory (Azure AD) no seu inquilino que terão acesso aos grupos de recursos delegados e/ou subscrições para os clientes que adquirem esse plano. É importante estar ciente de que qualquer grupo (ou utilizador ou diretor de serviço) que inclua terá as mesmas permissões para cada cliente que comprar o plano. Para atribuir diferentes grupos para trabalhar com cada cliente, terá de publicar um [plano privado](../../marketplace/private-offers.md) separado que seja exclusivo de cada cliente.

## <a name="publish-your-offer"></a>Publique a sua oferta

Uma vez concluídas todas as secções, o seu próximo passo é publicar a oferta ao Azure Marketplace. Selecione o botão **Publicar** para iniciar o processo de fazer a sua oferta ao vivo. Mais informações sobre este processo, podem ser encontradas [aqui.](../../marketplace/partner-center-portal/create-new-managed-service-offer.md#publish) 

Pode [publicar uma versão atualizada da sua oferta a](../..//marketplace/partner-center-portal/update-existing-offer.md) qualquer momento. Por exemplo, pode querer adicionar uma nova definição de papel a uma oferta publicada anteriormente. Quando o fizer, os clientes que já adicionaram a oferta verão um ícone na página de fornecedores de [**Serviços**](view-manage-service-providers.md) no portal Azure que lhes permite saber que uma atualização está disponível. Cada cliente poderá [rever as alterações](view-manage-service-providers.md#update-service-provider-offers) e decidir se pretende atualizar para a nova versão. 

## <a name="the-customer-onboarding-process"></a>O processo de embarque do cliente

Depois de um cliente adicionar a sua oferta, eles serão capazes [de delegar uma ou mais subscrições específicas ou grupos de recursos](view-manage-service-providers.md#delegate-resources), que serão então a bordo para a gestão de recursos delegados Azure. Se um cliente aceitar uma oferta mas ainda não tiver delegado quaisquer recursos, verá uma nota no topo da secção de **ofertas** do Fornecedor da página de prestadores de [**serviços**](view-manage-service-providers.md) no portal Azure.

> [!IMPORTANT]
> A delegação deve ser feita por uma conta não hóspede no arrendatário do cliente que tenha o [papel incorporado do Proprietário](../../role-based-access-control/built-in-roles.md#owner) para a subscrição a bordo (ou que contenha os grupos de recursos que estão a ser a bordo). Para ver todos os utilizadores que possam delegar a subscrição, um utilizador no arrendatário do cliente pode selecionar a subscrição no portal Azure, abrir o **controlo de acesso (IAM)** e [ver todos os utilizadores com a função Proprietário.](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription)

Uma vez que o cliente delega uma subscrição (ou um ou mais grupos de recursos dentro de uma subscrição), o fornecedor de recursos **Microsoft.ManagedServices** será registado para essa subscrição, e os utilizadores no seu inquilino poderão aceder aos recursos delegados de acordo com as autorizações da sua oferta.

## <a name="next-steps"></a>Próximos passos

- Conheça o [Mercado Comercial.](../../marketplace/partner-center-portal/commercial-marketplace-overview.md)
- Conheça as [experiências de gestão de inquilinos cruzados.](../concepts/cross-tenant-management-experience.md)
- [Ver e gerir clientes](view-manage-customers.md) indo para **os meus clientes** no portal Azure.
