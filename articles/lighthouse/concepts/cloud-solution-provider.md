---
title: Considerações sobre o programa do Fornecedor de Soluções Cloud
description: Para os parceiros csp, a gestão de recursos delegados da Azure ajuda a melhorar a segurança e o controlo, permitindo permissões granulares.
ms.date: 12/18/2019
ms.topic: conceptual
ms.openlocfilehash: 66ea74751f12a499a1e2d9e083497da31746e3c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75456907"
---
# <a name="azure-lighthouse-and-the-cloud-solution-provider-program"></a>Farol Azure e o programa Cloud Solution Provider

Se é um parceiro [CSP (Cloud Solution Provider),](https://docs.microsoft.com/partner-center/csp-overview) já pode aceder às subscrições Azure criadas para os seus clientes através do programa CSP utilizando a funcionalidade [Administer On Behalf Of (AOBO).](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) Este acesso permite-lhe apoiar, configurar e gerir diretamente as subscrições dos seus clientes.

Com o [Farol Azure,](../overview.md)pode utilizar a gestão de recursos delegados do Azure juntamente com a AOBO. Isto ajuda a melhorar a segurança e reduz o acesso desnecessário, permitindo mais permissões granulares para os seus utilizadores. Também permite uma maior eficiência e escalabilidade, uma vez que os seus utilizadores podem trabalhar em várias subscrições de clientes usando um único login no seu inquilino.

> [!TIP]
> Para ajudar a salvaguardar os recursos dos clientes, certifique-se de rever e seguir [as nossas práticas de segurança recomendadas,](recommended-security-practices.md) juntamente com os requisitos de segurança do [parceiro.](https://docs.microsoft.com/partner-center/partner-security-requirements)

## <a name="administer-on-behalf-of-aobo"></a>Administrar em nome de (AOBO)

Com a AOBO, qualquer utilizador com o papel de [Agente Administrador](https://docs.microsoft.com/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) no seu inquilino terá acesso AOBO às subscrições azure que cria através do programa CSP. Qualquer utilizador que necessite de acesso a qualquer subscrição de clientes deve ser membro deste grupo. A AOBO não permite a flexibilidade para criar grupos distintos que trabalhem com diferentes clientes, ou para permitir diferentes funções para grupos ou utilizadores.

![Gestão de inquilinos usando ao AOBO](../media/csp-1.jpg)

## <a name="azure-delegated-resource-management"></a>Gestão de recursos delegados do Azure

Utilizando a gestão de recursos delegados do Azure, pode atribuir diferentes grupos a diferentes clientes ou funções, como mostra o diagrama seguinte. Uma vez que os utilizadores terão o nível de acesso adequado através da gestão de recursos delegados do Azure, pode reduzir o número de utilizadores que têm a função de Agente Administrador (e, portanto, têm acesso total ao AOBO). Isto ajuda a melhorar a segurança limitando o acesso desnecessário aos recursos dos seus clientes. Também lhe dá mais flexibilidade para gerir vários clientes em escala.

No embarque de uma subscrição que criou através do programa CSP segue os passos descritos no [Bordo uma subscrição à gestão de recursos delegados do Azure.](../how-to/onboard-customer.md) Qualquer utilizador que tenha o papel de Agente Administrador no seu inquilino pode realizar este embarque.

![Gestão de inquilinos utilizando a AOBO e a Azure delegada gestão de recursos](../media/csp-2.jpg)

> [!NOTE]
> A página [ **dos meus clientes** no portal Azure](../how-to/view-manage-customers.md) inclui agora uma secção **cloud solution provider (Preview),** que exibe informações e recursos de faturação para clientes CSP que assinaram o Contrato de [Cliente da Microsoft (MCA)](https://docs.microsoft.com/partner-center/confirm-customer-agreement) e estão [ao abrigo do plano Azure.](https://docs.microsoft.com/partner-center/azure-plan-get-started) Para mais informações, consulte Começar com a sua conta de [faturação do Microsoft Partner Agreement](../../billing/mpa-overview.md).
>
> Os clientes da CSP podem aparecer nesta secção, quer também tenham sido ou não a bordo para a gestão de recursos delegados do Azure. Se o fizerem, também aparecerão na secção **Clientes,** conforme descrito no [View e gerirão clientes e recursos delegados.](../how-to/view-manage-customers.md) Da mesma forma, um cliente CSP não tem de aparecer na secção **Cloud Solution Provider (Preview)** dos **meus clientes** para que possa embarcar para a gestão de recursos delegados do Azure.

## <a name="next-steps"></a>Passos seguintes

- Conheça [as experiências de gestão de inquilinos cruzados.](cross-tenant-management-experience.md)
- Saiba como [embarcar numa subscrição da gestão de recursos delegados do Azure.](../how-to/onboard-customer.md)
- Conheça o [programa Cloud Solution Provider](https://docs.microsoft.com/partner-center/csp-overview).
