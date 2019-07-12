---
title: Lighthouse do Azure e o programa de fornecedor de soluções Cloud
description: Ao utilizar o Azure, gestão de recursos é delegada, é importante considerar a segurança e controlo de acesso.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 09552c66d2dc841cff8b5cb52e26dc657568e08f
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809961"
---
# <a name="azure-lighthouse-and-the-cloud-solution-provider-program"></a>Lighthouse do Azure e o programa de fornecedor de soluções Cloud

Se for um [CSP (fornecedor de soluções Cloud)](https://docs.microsoft.com/partner-center/csp-overview) parceiro, pode acessar as subscrições do Azure criadas para os seus clientes através do programa CSP utilizando o [administrar em nome da (AOBO)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) funcionalidade. Este acesso permite-lhe suporte diretamente, configurar e gerir subscrições dos seus clientes.

O mecanismo AOBO concede acesso completo a ambientes de cliente. Com a gestão de recursos do Azure de delegados, juntamente com AOBO ajuda a melhorar a segurança, permitindo-lhe reduzir o acesso desnecessário, permitindo que as permissões mais granulares para os seus utilizadores. 

## <a name="administer-on-behalf-of-aobo"></a>Administrar em nome de (AOBO)

Com AOBO, qualquer utilizador com o [agente administrador](https://docs.microsoft.com/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) função no seu inquilino têm acesso AOBO a subscrições do Azure que criar através do programa CSP. Qualquer usuário que precisa de acesso a subscrições de qualquer parte dos clientes tem de ser membro deste grupo. AOBO não permite a flexibilidade para criar grupos distintos que funcionam com diferentes clientes ou para ativar as funções diferentes para grupos de usuários.

![Gestão de inquilino através de AOBO](../media/csp-1.jpg)

## <a name="azure-delegated-resource-management"></a>Gestão de recursos do Azure de delegado

Utilizar o Azure delegadas a gestão de recursos, pode atribuir grupos diferentes para diferentes clientes ou funções, como mostrado no diagrama seguinte. Uma vez que os utilizadores terão o nível de acesso através da gestão de recursos do Azure de delegado adequado, pode reduzir o número de utilizadores que tenham a função de administrador agente (e, portanto, têm acesso total de AOBO). Isto proporciona maior segurança ao limitar o acesso desnecessário aos recursos dos seus clientes. Também lhe dá mais flexibilidade para gerir vários clientes em escala.

Integração de uma subscrição que criou através do programa CSP segue os passos descritos em [carregar uma subscrição do Azure delegadas a gestão de recursos](../how-to/onboard-customer.md). Qualquer utilizador que tenha a função de administrador agente no seu inquilino, pode efetuar esta integração.

Tenha em atenção que para as subscrições criadas através dos programas CSP, pedidos de suporte podem ser gerados somente por usuários com a função de agente de administrador no inquilino do fornecedor de serviços. Utilizadores adicionados através da gestão de recursos do Azure de delegado não poderão abrir pedidos de suporte para recursos delegados nestas subscrições.

![Gestão de recursos de delegado de gestão de inquilino através de AOBO e Azure](../media/csp-2.jpg)

## <a name="partner-admin-link"></a>Ligação de administrador do parceiro

Pode associar o seu ID do Microsoft Partner Network (MPN) com as suas subscrições integrado para controlar o impacto de envolvimento com o cliente.

Se [publicar uma oferta de serviços geridos no Azure Marketplace](../how-to/publish-managed-services-offers.md), seu ID MPN está associado ao seu perfil do publicador e é automaticamente associado com a oferta. Receita gerada por recursos do Azure através desta oferta, em seguida, irá ser atribuída à sua organização. Parceiro de sistemas, como o Centro de parceiros ou MPN de relatórios, a atribuição aparecerá como ligação de administrador de parceiro (PAL).

Se [integrar clientes para o Azure delegadas a gestão de recursos com modelos Azure Resource Manager](../how-to/onboard-customer.md), ainda pode associar o seu ID MPN para receber o reconhecimento de seu impacto no envolvimento com o cliente, mas terá Para fazê-lo manualmente. Para mais informações, veja [ligar um ID de parceiro a suas contas do Azure](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started). 

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [experiências de gestão entre inquilinos](cross-tenant-management-experience.md).
- Saiba mais sobre o [programa Cloud Solution Provider](https://docs.microsoft.com/partner-center/csp-overview).
