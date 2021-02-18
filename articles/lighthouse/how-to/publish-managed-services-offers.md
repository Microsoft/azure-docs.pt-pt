---
title: Publicar uma oferta de Serviço Gerido para o Azure Marketplace
description: Saiba como publicar uma oferta de Serviço Gerido que a bordo dos clientes para o Farol de Azure.
ms.date: 02/17/2021
ms.topic: how-to
ms.openlocfilehash: b906ba9961ae4f77ca16890fd0a9036123343111
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101092140"
---
# <a name="publish-a-managed-service-offer-to-azure-marketplace"></a>Publicar uma oferta de Serviço Gerido para o Azure Marketplace

Neste artigo, você aprenderá a publicar uma oferta de Serviço Gerido público ou privado ao [Azure Marketplace](https://azuremarketplace.microsoft.com) usando o programa [De Marketplace Comercial](../../marketplace/overview.md) no Partner Center. Os clientes que comprarem a oferta irão então delegar subscrições ou grupos de recursos, permitindo-lhe geri-las através [do Farol de Azure.](../overview.md)

## <a name="publishing-requirements"></a>Requisitos de publicação

Precisa de ter uma conta válida [no Partner Center](../../marketplace/partner-center-portal/create-account.md) para criar e publicar ofertas. Se ainda não tiver uma conta, o processo de inscrição irá [levá-lo](https://aka.ms/joinmarketplace) através dos passos de criação de uma conta no Partner Center e inscrição no programa Mercado Comercial.

De acordo com os [requisitos de certificação do Serviço Gerido,](/legal/marketplace/certification-policies#700-managed-services)você deve ter um [nível](/partner-center/learn-about-competencies) de competência silver ou Gold Cloud Platform ou ser um [Azure Expert MSP](https://partner.microsoft.com/membership/azure-expert-msp) para publicar uma oferta de Serviço Gerido. Também tem de [introduzir um destino de chumbo que irá criar um registo no seu sistema CRM](../../marketplace/plan-managed-service-offer.md#customer-leads) sempre que um cliente implementar a sua oferta.

Se não quiser publicar uma oferta ao Azure Marketplace, ou não cumprir todos os requisitos, pode embarcar manualmente os clientes utilizando modelos do Azure Resource Manager. Para mais informações, consulte [a bordo de um cliente do Farol Azure.](onboard-customer.md)

A tabela a seguir pode ajudar a determinar se devem ou não embarcar clientes publicando uma oferta de Serviço Gerido ou utilizando modelos de Gestor de Recursos Azure.

|**Consideração**  |**Oferta de Serviço Gerido**  |**Modelos de ARM**  |
|---------|---------|---------|
|Requer [conta partner center](../../marketplace/partner-center-portal/create-account.md)   |Yes         |No        |
|Requer [nível](/partner-center/learn-about-competencies) de competência da Plataforma Cloud prateada ou dourada ou [MSP expert Azure](https://partner.microsoft.com/membership/azure-expert-msp)      |Yes         |No         |
|Disponível para novos clientes através do Azure Marketplace     |Yes     |No       |
|Pode limitar a oferta a clientes específicos     |Sim (apenas com ofertas privadas, que não podem ser usadas com subscrições estabelecidas através de um revendedor do programa Cloud Solution Provider (CSP)         |Yes         |
|Requer aceitação do cliente no portal Azure     |Yes     |No   |
|Pode usar a automatização para embarcar várias subscrições, grupos de recursos ou clientes |No     |Yes    |
|Acesso imediato a novas funções incorporadas e características do Farol Azure     |Nem sempre (geralmente disponível após algum atraso)         |Yes         |

> [!NOTE]
> As ofertas de Serviço Gerido podem não estar disponíveis no Governo de Azure e noutras nuvens nacionais.

## <a name="create-your-offer"></a>Crie a sua oferta

Para instruções detalhadas sobre como criar a sua oferta, incluindo todas as informações e bens que necessitará de fornecer, consulte [Create a Managed Service offer](../../marketplace/create-managed-service-offer.md).

Para conhecer o processo de publicação geral, reveja a documentação do [Mercado Comercial.](../../marketplace/overview.md) Deve também rever as [políticas de certificação](/legal/marketplace/certification-policies)do mercado comercial, nomeadamente a secção [serviços geridos.](/legal/marketplace/certification-policies#700-managed-services)

Uma vez que um cliente adicione a sua oferta, eles serão capazes de delegar uma ou mais subscrições ou grupos de recursos, que serão então [a bordo do Farol de Azure.](#the-customer-onboarding-process)

> [!IMPORTANT]
> Cada plano de uma oferta de Serviço Gerido inclui uma secção **De Detalhes Manifesto,** onde define as entidades do Azure Ative Directory (Azure AD) no seu inquilino que terão acesso aos grupos de recursos delegados e/ou subscrições para os clientes que adquirem esse plano. É importante estar ciente de que qualquer grupo (ou utilizador ou diretor de serviço) que inclua terá as mesmas permissões para cada cliente que comprar o plano. Para atribuir diferentes grupos para trabalhar com cada cliente, pode publicar um [plano privado](../../marketplace/private-offers.md) separado que seja exclusivo de cada cliente. Tenha em mente que os planos privados não são suportados com subscrições estabelecidas através de um revendedor do programa Cloud Solution Provider (CSP).

## <a name="publish-your-offer"></a>Publique a sua oferta

Uma vez concluídas todas as secções, o seu próximo passo é publicar a oferta ao Azure Marketplace. Selecione o botão **Publicar** para iniciar o processo de fazer a sua oferta ao vivo. Mais informações sobre este processo podem ser [encontradas aqui.](../../marketplace/review-publish-offer.md)

Pode [publicar uma versão atualizada da sua oferta a](../..//marketplace/partner-center-portal/update-existing-offer.md) qualquer momento. Por exemplo, pode querer adicionar uma nova definição de papel a uma oferta publicada anteriormente. Quando o fizer, os clientes que já adicionaram a oferta verão um ícone na página de fornecedores de [**Serviços**](view-manage-service-providers.md) no portal Azure que lhes permite saber que uma atualização está disponível. Cada cliente poderá [rever as alterações](view-manage-service-providers.md#update-service-provider-offers) e decidir se pretende atualizar para a nova versão. 

## <a name="the-customer-onboarding-process"></a>O processo de embarque do cliente

Depois de um cliente adicionar a sua oferta, eles serão capazes [de delegar uma ou mais subscrições específicas ou grupos de recursos](view-manage-service-providers.md#delegate-resources), que serão depois a bordo do Farol de Azure. Se um cliente aceitar uma oferta mas ainda não tiver delegado quaisquer recursos, verá uma nota no topo da secção de **ofertas** do Fornecedor da página de prestadores de [**serviços**](view-manage-service-providers.md) no portal Azure.

> [!IMPORTANT]
> A delegação deve ser feita por uma conta não hóspede no arrendatário do cliente que tenha um papel com a `Microsoft.Authorization/roleAssignments/write` permissão, como o [Proprietário,](../../role-based-access-control/built-in-roles.md#owner)para que a subscrição seja a bordo (ou que contenha os grupos de recursos que estão a ser a bordo). Para encontrar utilizadores que possam delegar a subscrição, um utilizador no arrendatário do cliente pode selecionar a subscrição no portal Azure, abrir o **controlo de acesso (IAM)** e [ver todos os utilizadores com a função Proprietário](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).

Uma vez que o cliente delega uma subscrição (ou um ou mais grupos de recursos dentro de uma subscrição), o fornecedor de recursos **Microsoft.ManagedServices** será registado para essa subscrição, e os utilizadores no seu inquilino poderão aceder aos recursos delegados de acordo com as autorizações da sua oferta.

## <a name="next-steps"></a>Passos seguintes

- Conheça o [Mercado Comercial.](../../marketplace/overview.md)
- [Ligue o seu ID parceiro](partner-earned-credit.md) para rastrear o seu impacto através dos compromissos com os clientes.
- Conheça as [experiências de gestão de inquilinos cruzados.](../concepts/cross-tenant-management-experience.md)
- [Ver e gerir clientes](view-manage-customers.md) indo para **os meus clientes** no portal Azure.
