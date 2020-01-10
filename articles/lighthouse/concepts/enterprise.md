---
title: Azure Lighthouse em cenários empresariais
description: Os recursos do Azure Lighthouse podem ser usados para simplificar o gerenciamento entre locatários em uma empresa que usa vários locatários do Azure AD.
ms.date: 09/25/2019
ms.topic: conceptual
ms.openlocfilehash: 91089a6fb1a965191489e87027ef508c7ebe2aa2
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75749205"
---
# <a name="azure-lighthouse-in-enterprise-scenarios"></a>Azure Lighthouse em cenários empresariais

O cenário mais comum para o [Azure Lighthouse](../overview.md) é um provedor de serviços que gerencia recursos em seus locatários do Azure Active Directory (Azure AD) de seus clientes. No entanto, os recursos do Azure Lighthouse também podem ser usados para simplificar o gerenciamento entre locatários em uma empresa que usa vários locatários do Azure AD.

## <a name="single-vs-multiple-tenants"></a>Locatários únicos versus múltiplos

Para a maioria das organizações, o gerenciamento é mais fácil com um único locatário do Azure AD. Ter todos os recursos em um locatário permite a centralização de tarefas de gerenciamento por usuários designados, grupos de usuários ou entidades de serviço dentro desse locatário. É recomendável usar um locatário para sua organização sempre que possível.

Ao mesmo tempo, há situações que podem exigir que uma organização mantenha vários locatários do Azure AD. Em alguns casos, isso pode ser uma situação temporária, como quando as aquisições ocorreram e uma estratégia de consolidação de locatário de longo prazo levará algum tempo para definir. Uma organização também pode precisar manter vários locatários em uma base contínua (devido a subsidiárias totalmente independentes, requisitos geográficos ou legais e assim por diante). Nos casos em que uma arquitetura multilocatário é necessária, o gerenciamento de recursos delegado do Azure pode ser usado para centralizar e simplificar as operações de gerenciamento. As assinaturas de vários locatários podem ser integradas para o [Gerenciamento de recursos delegado do Azure](azure-delegated-resource-management.md), permitindo que usuários designados em um locatário de gerenciamento executem [funções de gerenciamento entre locatários](cross-tenant-management-experience.md) de maneira centralizada e escalonável.

## <a name="tenant-management-architecture"></a>Arquitetura de gerenciamento de locatário

Ao centralizar as operações de gerenciamento em vários locatários, você precisará determinar qual locatário incluirá os usuários que executam operações de gerenciamento para os outros locatários. Em outras palavras, será necessário determinar qual locatário será o locatário de gerenciamento para outros locatários.

Por exemplo, digamos que sua organização tenha um único locatário que chamarei *de locatário a*. Em seguida, sua organização adquire dois locatários adicionais, o *locatário B* e o *locatário C*, e você tem motivos comerciais que exigem que você os mantenha como locatários separados.

Sua organização deseja usar as mesmas definições de política, práticas de backup e processos de segurança em todos os locatários. Como você já tem usuários (incluindo grupos de usuários e entidades de serviço) responsáveis por executar essas tarefas no locatário A, é possível integrar todas as assinaturas no locatário B e no locatário C para que os mesmos usuários no locatário A possam executá-las tarefa.

![Usuários no locatário A Gerenciando recursos no locatário B e no locatário C](../media/enterprise-azure-lighthouse.jpg)

## <a name="security-and-access-considerations"></a>Considerações sobre segurança e acesso

Na maioria dos cenários empresariais, você desejará delegar uma assinatura completa para o gerenciamento de recursos delegado do Azure, embora também possa delegar apenas grupos de recursos específicos dentro de uma assinatura.

De qualquer forma, certifique-se de [seguir o princípio de privilégios mínimos ao definir quais usuários terão acesso aos recursos](recommended-security-practices.md#assign-permissions-to-groups-using-the-principle-of-least-privilege). Isso ajuda a garantir que os usuários tenham apenas as permissões necessárias para executar as tarefas necessárias e reduz a chance de erros inadvertidos.

O Azure Lighthouse e o gerenciamento de recursos delegado do Azure fornecem apenas links lógicos entre um locatário de gerenciamento e locatários gerenciados, em vez de mover dados ou recursos fisicamente. Além disso, o acesso sempre entra apenas em uma direção, do locatário de gerenciamento para os locatários gerenciados.  Os usuários e grupos no locatário de gerenciamento devem continuar a usar a autenticação multifator ao executar operações de gerenciamento em recursos de locatário gerenciados.

Empresas com governança interna ou externa e guardrails de conformidade podem usar [os logs de atividades do Azure](../../azure-monitor/platform/platform-logs-overview.md) para atender aos requisitos de transparência. Quando os locatários empresariais tiverem estabelecido o gerenciamento e as relações de locatário gerenciadas, os usuários em cada locatário poderão monitorar e obter visibilidade das ações executadas pelos usuários no outro locatário exibindo a atividade registrada.

## <a name="onboarding-process-considerations"></a>Considerações sobre processo de integração

As assinaturas (ou grupos de recursos em uma assinatura) podem ser integradas ao gerenciamento de recursos delegado do Azure por meio da implantação de modelos de Azure Resource Manager ou por meio de ofertas de serviços gerenciados publicados no Azure Marketplace, seja de forma privada ou publicamente.

Como os usuários empresariais normalmente serão capazes de obter acesso direto aos locatários da empresa, e não há necessidade de comercializar ou promover uma oferta de gerenciamento, geralmente é mais rápido e mais simples implantar diretamente com modelos de Azure Resource Manager. Embora possamos nos referimos aos provedores de serviços e clientes nas [diretrizes de integração](../how-to/onboard-customer.md), as empresas podem usar os mesmos processos.

Se preferir, os locatários dentro de uma empresa podem ser integrados por meio da [publicação de uma oferta de serviços gerenciados no Azure Marketplace](../how-to/publish-managed-services-offers.md). Para garantir que a oferta só esteja disponível para os locatários apropriados, certifique-se de que seus planos estejam marcados como particulares. Com um plano particular, você pode fornecer as IDs de assinatura para cada locatário que planeja carregar e ninguém mais poderá obter sua oferta.

## <a name="terminology-notes"></a>Notas de terminologia

Para o gerenciamento entre locatários dentro da empresa, as referências a provedores de serviço na documentação do Azure Lighthouse podem ser compreendidas para aplicar ao locatário de gerenciamento em uma empresa, ou seja, o locatário que inclui os usuários que gerenciarão os recursos em outros locatários por meio do gerenciamento de recursos delegado do Azure. Da mesma forma, as referências a clientes podem ser compreendidas para se aplicar aos locatários que estão delegando recursos a serem gerenciados por meio de usuários no locatário de gerenciamento.

Por exemplo, no exemplo descrito acima, o locatário A pode ser considerado como o locatário do provedor de serviço (o locatário de gerenciamento) e o locatário B e o locatário C podem ser considerados como locatários do cliente.

Nesse exemplo, o locatário que um usuário com as permissões apropriadas pode [Exibir e gerenciar recursos delegados](../how-to/view-manage-customers.md) na página **meus clientes** do portal do Azure. Da mesma forma, os usuários do locatário B e do locatário C com as permissões apropriadas podem [Exibir e gerenciar os recursos que foram delegados](../how-to/view-manage-service-providers.md) para o locatário a na página **provedores de serviço** do portal do Azure.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre as [experiências de gerenciamento entre locatários](cross-tenant-management-experience.md).
- Saiba mais sobre a [Gestão de recursos delegados do Azure](azure-delegated-resource-management.md).