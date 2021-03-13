---
title: Considerações sobre o programa do Fornecedor de Soluções Cloud
description: Para os parceiros da CSP, a gestão de recursos delegada da Azure ajuda a melhorar a segurança e o controlo, permitindo permissões granulares.
ms.date: 03/12/2021
ms.topic: conceptual
ms.openlocfilehash: 8736cf913739f2bd16fb519aed98fd336f6876a5
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2021
ms.locfileid: "103419393"
---
# <a name="azure-lighthouse-and-the-cloud-solution-provider-program"></a>Farol Azure e o programa Cloud Solution Provider

Se é um parceiro [CSP (Cloud Solution Provider),](/partner-center/csp-overview) já pode aceder às subscrições Azure criadas para os seus clientes através do programa CSP utilizando a funcionalidade [Gestão Em Nome da AOBO.](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) Este acesso permite-lhe apoiar, configurar e gerir diretamente as subscrições dos seus clientes.

Com [o Azure Lighthouse,](../overview.md)pode utilizar a gestão de recursos delegada da Azure juntamente com a AOBO. Isto ajuda a melhorar a segurança e reduz o acesso desnecessário, permitindo mais permissões granulares para os seus utilizadores. Também permite uma maior eficiência e escalabilidade, uma vez que os seus utilizadores podem trabalhar em várias subscrições de clientes usando um único login no seu inquilino.

> [!TIP]
> Para ajudar a salvaguardar os recursos do cliente, certifique-se de rever e seguir as [nossas práticas de segurança recomendadas](recommended-security-practices.md) juntamente com os [requisitos de segurança](/partner-center/partner-security-requirements)do parceiro.

## <a name="administer-on-behalf-of-aobo"></a>Administração em nome da (AOBO)

Com a AOBO, qualquer utilizador com o papel [de Agente Administrador](/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) no seu inquilino terá acesso ao AOBO às subscrições Azure que cria através do programa CSP. Todos os utilizadores que necessitem de acesso a subscrições de qualquer cliente devem ser membros deste grupo. A AOBO não permite a flexibilidade para criar grupos distintos que trabalham com diferentes clientes, ou para permitir diferentes funções para grupos ou utilizadores.

![Diagrama mostrando gestão de inquilinos usando AOBO.](../media/csp-1.jpg)

## <a name="azure-delegated-resource-management"></a>Gestão de recursos delegados do Azure

Utilizando o Farol Azure, pode atribuir diferentes grupos a diferentes clientes ou funções, como mostra o diagrama seguinte. Uma vez que os utilizadores terão o nível de acesso adequado através da gestão de recursos delegados do Azure, pode reduzir o número de utilizadores que têm a função de Agente Administrador (e assim ter acesso total ao AOBO). Isto ajuda a melhorar a segurança limitando o acesso desnecessário aos recursos dos seus clientes. Também lhe dá mais flexibilidade para gerir vários clientes em escala.

A bordo de uma subscrição que criou através do programa CSP segue os passos descritos a [bordo de uma subscrição do Farol Azure.](../how-to/onboard-customer.md) Qualquer utilizador que tenha o papel de Agente Administrativo no seu inquilino pode realizar este embarque.

![Diagrama mostrando a gestão dos inquilinos usando a AOBO e a Azure delegado de gestão de recursos.](../media/csp-2.jpg)

> [!TIP]
> [As ofertas de Serviços Geridos](managed-services-offers.md) com planos privados não são suportadas com subscrições estabelecidas através de um revendedor do programa Cloud Solution Provider (CSP). Pode embarcar nestas subscrições do Azure Lighthouse [utilizando modelos do Azure Resource Manager](../how-to/onboard-customer.md).

> [!NOTE]
> A página [ **dos meus clientes** no portal Azure](../how-to/view-manage-customers.md) inclui agora uma secção **cloud solution provider (Preview),** que exibe informações e recursos de faturação para clientes da CSP que [assinaram o Microsoft Customer Agreement (MCA)](/partner-center/confirm-customer-agreement) e estão [ao abrigo do plano Azure.](/partner-center/azure-plan-get-started) Para obter mais informações, consulte [Começar com a sua conta de faturação do Microsoft Partner Agreement](../../cost-management-billing/understand/mpa-overview.md).
>
> Os clientes da CSP podem aparecer nesta secção, quer também tenham sido acedidos ao Farol de Azure. Se o fizerem, também aparecerão na secção **Clientes,** conforme descrito no [View e gerirão clientes e recursos delegados.](../how-to/view-manage-customers.md) Da mesma forma, um cliente CSP não tem de aparecer na secção **Cloud Solution Provider (Preview)** dos **meus clientes** para que possa embarcar no Farol de Azure.

## <a name="next-steps"></a>Passos seguintes

- Conheça as [experiências de gestão de inquilinos cruzados.](cross-tenant-management-experience.md)
- Saiba como embarcar [numa subscrição do Azure Lighthouse.](../how-to/onboard-customer.md)
- Saiba mais sobre o [programa Cloud Solution Provider](/partner-center/csp-overview).
