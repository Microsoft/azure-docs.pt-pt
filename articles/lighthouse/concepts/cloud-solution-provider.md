---
title: Considerações sobre o programa do Fornecedor de Soluções Cloud
description: Para parceiros CSP, o gerenciamento de recursos delegado do Azure ajuda a melhorar a segurança e o controle habilitando permissões granulares.
ms.date: 12/18/2019
ms.topic: conceptual
ms.openlocfilehash: 66ea74751f12a499a1e2d9e083497da31746e3c7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456907"
---
# <a name="azure-lighthouse-and-the-cloud-solution-provider-program"></a>O Azure Lighthouse e o programa de provedor de soluções na nuvem

Se você for um parceiro [CSP (provedor de soluções na nuvem)](https://docs.microsoft.com/partner-center/csp-overview) , você já poderá acessar as assinaturas do Azure criadas para seus clientes por meio do programa CSP usando a funcionalidade [administrar em nome de (Aobo)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) . Esse acesso permite que você dê suporte, configure e gerencie diretamente as assinaturas de seus clientes.

Com o [Azure Lighthouse](../overview.md), você pode usar o gerenciamento de recursos delegado do Azure junto com o AOBO. Isso ajuda a melhorar a segurança e reduz o acesso desnecessário habilitando permissões mais granulares para seus usuários. Ele também permite maior eficiência e escalabilidade, pois os usuários podem trabalhar em várias assinaturas de clientes usando um único logon em seu locatário.

> [!TIP]
> Para ajudar a proteger os recursos do cliente, leia e siga nossas [práticas recomendadas de segurança](recommended-security-practices.md) junto com os [requisitos de segurança do parceiro](https://docs.microsoft.com/partner-center/partner-security-requirements).

## <a name="administer-on-behalf-of-aobo"></a>Administrar em nome de (AOBO)

Com o AOBO, qualquer usuário com a função [agente de administração](https://docs.microsoft.com/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) em seu locatário terá acesso Aobo às assinaturas do Azure que você criar por meio do programa CSP. Todos os usuários que precisam acessar as assinaturas de qualquer cliente devem ser membros desse grupo. O AOBO não permite a flexibilidade de criar grupos distintos que funcionam com clientes diferentes ou para habilitar funções diferentes para grupos ou usuários.

![Gerenciamento de locatários usando AOBO](../media/csp-1.jpg)

## <a name="azure-delegated-resource-management"></a>Gestão de recursos delegados do Azure

Usando o gerenciamento de recursos delegado do Azure, você pode atribuir diferentes grupos a clientes ou funções diferentes, conforme mostrado no diagrama a seguir. Como os usuários terão o nível apropriado de acesso por meio do gerenciamento de recursos delegado do Azure, você pode reduzir o número de usuários que têm a função de agente de administrador (e, portanto, ter acesso total a AOBO). Isso ajuda a melhorar a segurança limitando o acesso desnecessário aos recursos de seus clientes. Ele também proporciona mais flexibilidade para gerenciar vários clientes em escala.

A integração de uma assinatura que você criou por meio do programa CSP segue as etapas descritas em [integração de uma assinatura ao gerenciamento de recursos delegado do Azure](../how-to/onboard-customer.md). Qualquer usuário que tenha a função de agente de administração em seu locatário pode realizar essa integração.

![Gerenciamento de locatários usando o AOBO e o gerenciamento de recursos delegado do Azure](../media/csp-2.jpg)

> [!NOTE]
> A [página **meus clientes** no portal do Azure](../how-to/view-manage-customers.md) agora inclui uma seção **provedor de soluções na nuvem (versão prévia)** , que exibe informações de cobrança e recursos para clientes do CSP que [assinaram o MCA (contrato de cliente da Microsoft)](https://docs.microsoft.com/partner-center/confirm-customer-agreement) e estão [no plano do Azure](https://docs.microsoft.com/partner-center/azure-plan-get-started). Para obter mais informações, consulte Introdução [à sua conta de cobrança do Microsoft Partner Agreement](../../billing/mpa-overview.md).
>
> Os clientes do CSP podem aparecer nesta seção se também tiverem sido integrados ao gerenciamento de recursos delegado do Azure. Se eles tiverem, eles também aparecerão na seção **clientes** , conforme descrito em [Exibir e gerenciar clientes e recursos delegados](../how-to/view-manage-customers.md). Da mesma forma, um cliente CSP não precisa aparecer na seção **provedor de soluções na nuvem (versão prévia)** de **meus clientes** para que você possa integrá-los para o gerenciamento de recursos delegado do Azure.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre as [experiências de gerenciamento entre locatários](cross-tenant-management-experience.md).
- Saiba como integrar [uma assinatura ao gerenciamento de recursos delegado do Azure](../how-to/onboard-customer.md).
- Saiba mais sobre o [programa provedor de soluções na nuvem](https://docs.microsoft.com/partner-center/csp-overview).
