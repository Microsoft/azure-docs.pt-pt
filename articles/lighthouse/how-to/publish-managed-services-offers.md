---
title: Publicar uma oferta de Serviço Gerido ao Azure Marketplace
description: Saiba como publicar uma oferta de Serviço Gerido que a bordo dos clientes para a gestão de recursos delegados do Azure.
ms.date: 05/04/2020
ms.topic: conceptual
ms.openlocfilehash: 04da33d0232371f4175a935bf1e35925376babbc
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82788744"
---
# <a name="publish-a-managed-service-offer-to-azure-marketplace"></a>Publicar uma oferta de Serviço Gerido ao Azure Marketplace

Neste artigo, você aprenderá a publicar uma oferta de Serviço Gerido público ou privado ao [Azure Marketplace](https://azuremarketplace.microsoft.com) usando o programa [Mercado Comercial](../../marketplace/partner-center-portal/commercial-marketplace-overview.md) no Partner Center. Os clientes que adquirem a oferta podem então embarcar subscrições e grupos de recursos para a [gestão de recursos delegados do Azure.](../concepts/azure-delegated-resource-management.md)

## <a name="publishing-requirements"></a>Requisitos de publicação

Precisa de ter uma conta válida [no Partner Center](../../marketplace/partner-center-portal/create-account.md) para criar e publicar ofertas. Se ainda não tiver uma conta, o processo de inscrição irá [levá-lo](https://aka.ms/joinmarketplace) através dos passos de criação de uma conta no Partner Center e de se inscrever no programa Mercado Comercial.

De acordo com os requisitos de certificação do [Serviço Gerido,](https://docs.microsoft.com/legal/marketplace/certification-policies#7004-business-requirements)deve ter um [nível](https://docs.microsoft.com/partner-center/learn-about-competencies) de competência da Plataforma Silver ou Gold Cloud ou ser um [MSP de Peritos Azure](https://partner.microsoft.com/membership/azure-expert-msp) para publicar uma oferta de Serviço Gerido.

O id da Microsoft Partner Network (MPN) será [automaticamente associado às](../../billing/billing-partner-admin-link-started.md) ofertas que publica para acompanhar o seu impacto através dos compromissos dos clientes.

> [!NOTE]
> Se não quiser publicar uma oferta para o Azure Marketplace, pode embarcar nos clientes manualmente utilizando modelos do Gestor de Recursos Do Azure. Para mais informações, consulte [A bordo um cliente para a gestão de recursos delegados do Azure.](onboard-customer.md)

## <a name="create-your-offer"></a>Crie a sua oferta

Para obter instruções detalhadas sobre como criar a sua oferta, incluindo todas as informações e ativos que necessitará de fornecer, consulte [Criar uma oferta](../../marketplace/partner-center-portal/create-new-managed-service-offer.md)de Serviço Gerido .

Para conhecer o processo de publicação geral, consulte [o Azure Marketplace e o AppSource Publishing Guide](../../marketplace/marketplace-publishers-guide.md). Deve também rever as políticas de certificação do [mercado comercial,](https://docs.microsoft.com/legal/marketplace/certification-policies)nomeadamente a secção [de Serviços Geridos.](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)

Assim que um cliente adicionar a sua oferta, eles poderão delegar uma ou mais subscrições ou grupos de recursos, que serão depois [a bordo para a gestão de recursos delegados do Azure.](#the-customer-onboarding-process)

> [!IMPORTANT]
> Cada plano numa oferta de Serviço Gerido inclui uma secção **de Detalhes Manifestos,** onde define as entidades azure Ative Directory (Azure AD) no seu inquilino que terão acesso aos grupos de recursos delegados e/ou subscrições para clientes que adquiram esse plano. É importante estar ciente de que qualquer grupo (ou utilizador ou diretor de serviço) que você inclua terá as mesmas permissões para cada cliente que comprar o plano. Para atribuir diferentes grupos para trabalhar com cada cliente, terá de publicar um [plano privado](../../marketplace/private-offers.md) separado que seja exclusivo de cada cliente.

## <a name="publish-your-offer"></a>Publique a sua oferta

Depois de ter concluído todas as secções, o seu próximo passo é publicar a oferta ao Azure Marketplace. Selecione o botão **Publicar** para iniciar o processo de tornar a sua oferta ao vivo. Mais informações sobre este processo, podem ser encontradas [aqui.](../../marketplace/partner-center-portal/create-new-managed-service-offer.md#publish) 

Pode [publicar uma versão atualizada da sua oferta](../..//marketplace/partner-center-portal/update-existing-offer.md) a qualquer momento. Por exemplo, pode querer adicionar uma nova definição de papel a uma oferta anteriormente publicada. Quando o fizer, os clientes que já adicionaram a oferta verão um ícone na página de fornecedores de [**Serviços**](view-manage-service-providers.md) no portal Azure que os permite saber que está disponível uma atualização. Cada cliente poderá [rever as alterações](view-manage-service-providers.md#update-service-provider-offers) e decidir se pretende atualizar para a nova versão. 

## <a name="the-customer-onboarding-process"></a>O processo de embarque do cliente

Depois de um cliente adicionar a sua oferta, eles poderão [delegar uma ou mais subscrições ou grupos de recursos específicos](view-manage-service-providers.md#delegate-resources), que serão depois a bordo para a gestão de recursos delegados do Azure. Se um cliente aceitou uma oferta mas ainda não delegou quaisquer recursos, verá uma nota no topo do **Fornecedor oferece** secção da página de fornecedores de [**serviços**](view-manage-service-providers.md) no portal Azure.

> [!IMPORTANT]
> A delegação deve ser feita por uma conta não hóspede no inquilino do cliente que tenha o [papel integrado](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) do Proprietário para a subscrição a bordo (ou que contenha os grupos de recursos que estão a bordo). Para ver todos os utilizadores que possam delegar a subscrição, um utilizador no inquilino do cliente pode selecionar a subscrição no portal Azure, abrir o controlo de **acesso (IAM),** e [ver todos os utilizadores com a função Proprietário.](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription)

Assim que o cliente delegar uma subscrição (ou um ou mais grupos de recursos dentro de uma subscrição), o fornecedor de recursos **Da Microsoft.ManagedServices** será registado para essa subscrição, e os utilizadores do seu inquilino poderão aceder aos recursos delegados de acordo com as autorizações da sua oferta.

## <a name="next-steps"></a>Passos seguintes

- Conheça o [Mercado Comercial.](../../marketplace/partner-center-portal/commercial-marketplace-overview.md)
- Conheça [as experiências de gestão de inquilinos cruzados.](../concepts/cross-tenant-management-experience.md)
- [Ver e gerir os clientes](view-manage-customers.md) indo para os meus **clientes** no portal Azure.
