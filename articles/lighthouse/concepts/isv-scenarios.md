---
title: Farol Azure em cenários ISV
description: As capacidades do Farol Azure podem ser usadas pelos ISVs para uma maior flexibilidade com as ofertas dos clientes.
ms.date: 12/18/2020
ms.topic: conceptual
ms.openlocfilehash: d6a12a51d360ad236563b871dbd94cc442ade434
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/19/2020
ms.locfileid: "97696386"
---
# <a name="azure-lighthouse-in-isv-scenarios"></a>Farol Azure em cenários ISV

Um cenário comum para [o Azure Lighthouse](../overview.md) é um prestador de serviços que gere recursos nos inquilinos Azure Ative Directory (Azure AD) dos seus clientes. As capacidades do Azure Lighthouse também podem ser usadas por Fornecedores independentes de Software (ISVs) usando ofertas baseadas em SaaS com os seus clientes. O Farol Azure pode ser especialmente útil para os ISVs que estão a oferecer serviços geridos ou suporte que requerem acesso ao âmbito de subscrição.

## <a name="managed-service-offers-in-azure-marketplace"></a>Ofertas de Serviço Gerido no Azure Marketplace

Como ISV, já pode ter publicado soluções para o Azure Marketplace. Se oferecer serviços geridos aos seus clientes, pode fazê-lo publicando uma oferta de Serviço Gerido. Estas ofertas simplificam o processo de embarque e tornam os seus serviços mais escaláveis para o maior número de clientes que for necessário. O Azure Lighthouse suporta uma vasta gama de [tarefas e cenários](cross-tenant-management-experience.md#enhanced-services-and-scenarios) de gestão que podem ser usados para fornecer valor aos seus clientes.

Para mais informações, consulte [publicar uma oferta de Serviço Gerido ao Azure Marketplace.](../how-to/publish-managed-services-offers.md)

## <a name="using-azure-lighthouse-with-azure-managed-applications"></a>Utilizando o Farol Azure com aplicações geridas pela Azure

[As aplicações geridas pela Azure](../../azure-resource-manager/managed-applications/overview.md) são outra forma de os ISVs poderem prestar serviços aos seus clientes. Pode utilizar o Farol Azure juntamente com as aplicações geridas aZure para permitir cenários melhorados.

Para mais informações, consulte [as aplicações geridas pelo Farol Azure e Azure.](managed-applications.md)

## <a name="saas-based-multi-tenant-offerings"></a>Ofertas multi-arrendatas baseadas em SaaS

Um cenário adicional é onde o ISV acolhe os recursos numa subscrição no seu próprio inquilino, e depois usa o Farol Azure para permitir que os clientes acedam a esses recursos. O cliente pode então fazer login no seu próprio inquilino e aceder a estes recursos conforme necessário. Os ISVs mantêm o seu IP no seu próprio inquilino, e podem usar os seus próprios planos de apoio para levantar bilhetes relacionados com a solução hospedada no seu inquilino, em vez de usar o plano do cliente. Uma vez que os recursos estão no inquilino do ISV, todas as ações podem ser realizadas diretamente pelo ISV, como iniciar sessão em VMs, instalar apps e executar tarefas de manutenção.

Neste cenário, os utilizadores do arrendatário do cliente têm essencialmente acesso como "inquilino gerente", mesmo que o cliente não esteja a gerir os recursos do ISV. Como estão a aceder diretamente ao inquilino do ISV, é importante conceder apenas as permissões mínimas necessárias, para que os clientes não possam inadvertidamente fazer alterações na solução ou outros recursos ISV.

Para permitir esta arquitetura, o ISV necessita de obter o ID do objeto para um grupo de utilizadores no inquilino AZure AD do cliente, juntamente com o seu ID do inquilino. Em seguida, o ISV constrói um modelo ARM que concede a este grupo de utilizadores as permissões apropriadas e [implementa-o na subscrição do ISV](../how-to/onboard-customer.md) que contém os recursos a que o cliente irá aceder.

## <a name="next-steps"></a>Passos seguintes

- Conheça as [experiências de gestão de inquilinos cruzados.](cross-tenant-management-experience.md)
- Saiba mais sobre a [Gestão de recursos delegados do Azure](azure-delegated-resource-management.md).