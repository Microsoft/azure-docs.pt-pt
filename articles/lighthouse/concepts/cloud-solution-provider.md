---
title: O Azure Lighthouse e o programa de provedor de soluções na nuvem
description: Ao usar o gerenciamento de recursos delegado do Azure, é importante considerar a segurança e o controle de acesso.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 08/22/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 399d2cb829c0425e3c9ee70a61cafde8568f903b
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012138"
---
# <a name="azure-lighthouse-and-the-cloud-solution-provider-program"></a>O Azure Lighthouse e o programa de provedor de soluções na nuvem

Se você for um parceiro [CSP (provedor de soluções na nuvem)](https://docs.microsoft.com/partner-center/csp-overview) , você já poderá acessar as assinaturas do Azure criadas para seus clientes por meio do programa CSP usando a funcionalidade [administrar em nome de (Aobo)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) . Esse acesso permite que você dê suporte, configure e gerencie diretamente as assinaturas de seus clientes.

Com o Azure Lighthouse, você pode usar o gerenciamento de recursos delegado do Azure junto com o AOBO. Isso ajuda a melhorar a segurança e reduz o acesso desnecessário habilitando permissões mais granulares para seus usuários. Ele também permite maior eficiência e escalabilidade, pois os usuários podem trabalhar em várias assinaturas de clientes usando um único logon em seu locatário.

## <a name="administer-on-behalf-of-aobo"></a>Administrar em nome de (AOBO)

Com o AOBO, qualquer usuário com a função [agente de administração](https://docs.microsoft.com/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) em seu locatário terá acesso Aobo às assinaturas do Azure que você criar por meio do programa CSP. Todos os usuários que precisam acessar as assinaturas de qualquer cliente devem ser membros desse grupo. O AOBO não permite a flexibilidade de criar grupos distintos que funcionam com clientes diferentes ou para habilitar funções diferentes para grupos ou usuários.

![Gerenciamento de locatários usando AOBO](../media/csp-1.jpg)

## <a name="azure-delegated-resource-management"></a>Gestão de recursos delegados do Azure

Usando o gerenciamento de recursos delegado do Azure, você pode atribuir diferentes grupos a clientes ou funções diferentes, conforme mostrado no diagrama a seguir. Como os usuários terão o nível apropriado de acesso por meio do gerenciamento de recursos delegado do Azure, você pode reduzir o número de usuários que têm a função de agente de administrador (e, portanto, ter acesso total a AOBO). Isso ajuda a melhorar a segurança limitando o acesso desnecessário aos recursos de seus clientes. Ele também proporciona mais flexibilidade para gerenciar vários clientes em escala.

A integração de uma assinatura que você criou por meio do programa CSP segue as etapas descritas em [integração de uma assinatura ao gerenciamento de recursos delegado do Azure](../how-to/onboard-customer.md). Qualquer usuário que tenha a função de agente de administração em seu locatário pode realizar essa integração.

![Gerenciamento de locatários usando o AOBO e o gerenciamento de recursos delegado do Azure](../media/csp-2.jpg)

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre as [experiências de gerenciamento entre locatários](cross-tenant-management-experience.md).
- Saiba como integrar [uma assinatura ao gerenciamento de recursos delegado do Azure](../how-to/onboard-customer.md).
- Saiba mais sobre o [programa provedor de soluções na nuvem](https://docs.microsoft.com/partner-center/csp-overview).
