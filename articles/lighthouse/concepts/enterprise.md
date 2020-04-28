---
title: Azure Lighthouse em cenários empresariais
description: As capacidades do Farol Azure podem ser usadas para simplificar a gestão de inquilinos transversais dentro de uma empresa que utiliza vários inquilinos da AD Azure.
ms.date: 09/25/2019
ms.topic: conceptual
ms.openlocfilehash: 91089a6fb1a965191489e87027ef508c7ebe2aa2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75749205"
---
# <a name="azure-lighthouse-in-enterprise-scenarios"></a>Azure Lighthouse em cenários empresariais

O cenário mais comum para o [Azure Lighthouse](../overview.md) é um prestador de serviços que gere recursos nos inquilinos do Azure Ative Directory (Azure AD) dos seus clientes. No entanto, as capacidades do Farol Azure também podem ser usadas para simplificar a gestão de inquilinos transversais dentro de uma empresa que utiliza vários inquilinos da AD Azure.

## <a name="single-vs-multiple-tenants"></a>Single vs. vários inquilinos

Para a maioria das organizações, a gestão é mais fácil com um único inquilino azure AD. Ter todos os recursos dentro de um inquilino permite a centralização de tarefas de gestão por utilizadores designados, grupos de utilizadores ou diretores de serviço dentro desse inquilino. Recomendamos a utilização de um inquilino para a sua organização sempre que possível.

Ao mesmo tempo, existem situações que podem exigir que uma organização mantenha vários inquilinos da AD Azure. Em alguns casos, esta pode ser uma situação temporária, uma vez que quando se realizaram aquisições e uma estratégia de consolidação de inquilinos a longo prazo levará algum tempo a definir. Uma organização pode igualmente ter de manter os múltiplos inquilinos numa base contínua (devido a filiais totalmente independentes, requisitos geográficos ou legais, e assim por diante). Nos casos em que é necessária uma arquitetura multi-arrendatária, a gestão de recursos delegados da Azure pode ser usada para centralizar e agilizar operações de gestão. As subscrições de vários inquilinos podem ser a bordo para a [gestão de recursos delegados do Azure,](azure-delegated-resource-management.md)permitindo aos utilizadores designados de um inquilino gerente desempenhar [funções de gestão de inquilinos cruzados](cross-tenant-management-experience.md) de forma centralizada e escalável.

## <a name="tenant-management-architecture"></a>Arquitetura de gestão de inquilinos

Ao centralizar as operações de gestão em vários inquilinos, terá de determinar qual o inquilino que incluirá os utilizadores que realizam operações de gestão para os outros inquilinos. Por outras palavras, você precisará determinar qual inquilino será o inquilino gerente de outros inquilinos.

Por exemplo, digamos que a sua organização tem um único inquilino a quem chamaremos *inquilino A.* A sua organização adquire então mais dois inquilinos, *inquilino B* e *Inquilino C,* e tem razões comerciais que exigem que os mantenha como inquilinos separados.

A sua organização quer usar as mesmas definições políticas, práticas de backup e processos de segurança em todos os inquilinos. Uma vez que já tem utilizadores (incluindo grupos de utilizadores e diretores de serviço) responsáveis pela realização destas tarefas dentro do Tenant A, pode embarcar em todas as subscrições dentro do Tenant B e Tenant C para que os mesmos utilizadores do Tenant A possam executar essas tarefas.

![Utilizadores em Inquilino A gerindo recursos em Inquilino B e Inquilino C](../media/enterprise-azure-lighthouse.jpg)

## <a name="security-and-access-considerations"></a>Considerações de segurança e acesso

Na maioria dos cenários empresariais, você vai querer delegar uma subscrição completa para a gestão de recursos delegados do Azure, embora você também possa delegar apenas grupos de recursos específicos dentro de uma subscrição.

Seja como for, não se esqueça [de seguir o princípio do menor privilégio ao definir quais os utilizadores que terão acesso aos recursos.](recommended-security-practices.md#assign-permissions-to-groups-using-the-principle-of-least-privilege) Fazê-lo ajuda a garantir que os utilizadores apenas possuem as permissões necessárias para executar as tarefas necessárias e reduz a possibilidade de erros inadvertidos.

O Azure Lighthouse e a Azure delegaram a gestão de recursos apenas fornecem ligações lógicas entre um inquilino gerente e inquilinos geridos, em vez de mover fisicamente dados ou recursos. Além disso, o acesso vai sempre numa direção, desde o inquilino gerente até aos inquilinos geridos.  Os utilizadores e grupos no inquilino gerente devem continuar a utilizar a autenticação multifactor ao realizar operações de gestão em recursos de inquilinos geridos.

As empresas com governação interna ou externa e guarda-costas de conformidade podem utilizar [registos da Atividade Azure](../../azure-monitor/platform/platform-logs-overview.md) para satisfazer os seus requisitos de transparência. Quando os inquilinos da empresa estabeleceram relações de gestão e gestão de inquilinos, os utilizadores de cada inquilino podem monitorizar e ganhar visibilidade às ações tomadas pelos utilizadores do outro inquilino, visualizando a atividade registada.

## <a name="onboarding-process-considerations"></a>Considerações de processo de embarque

As subscrições (ou grupos de recursos dentro de uma subscrição) podem ser a bordo da gestão de recursos delegados do Azure, quer através da implementação de modelos do Gestor de Recursos Azure, quer através de ofertas de Serviços Geridos publicadas no Azure Marketplace, privada ou publicamente.

Uma vez que os utilizadores empresariais normalmente poderão ter acesso direto aos inquilinos da empresa, e não há necessidade de comercializar ou promover uma oferta de gestão, é geralmente mais rápido e simples de implantar diretamente com os modelos do Gestor de Recursos Azure. Embora nos refiramos a prestadores de serviços e clientes na orientação de [embarque,](../how-to/onboard-customer.md)as empresas podem usar os mesmos processos.

Se preferir, os inquilinos dentro de uma empresa podem ser abordados [publicando uma oferta de Serviços Geridos ao Azure Marketplace.](../how-to/publish-managed-services-offers.md) Para garantir que a oferta só está disponível para os inquilinos apropriados, certifique-se de que os seus planos estão marcados como privados. Com um plano privado, você pode fornecer as IDs de subscrição para cada inquilino que você planeja embarcar, e ninguém mais será capaz de obter a sua oferta.

## <a name="terminology-notes"></a>Notas de terminologia

Para a gestão de inquilinos transversais dentro da empresa, as referências aos prestadores de serviços na documentação do Farol Azure podem ser entendidas como aplicáveis ao inquilino gerente dentro de uma empresa — ou seja, o inquilino que inclui os utilizadores que irão gerir recursos noutros inquilinos através da gestão de recursos delegados do Azure. Da mesma forma, as referências aos clientes podem ser entendidas como aplicáveis aos inquilinos que estão a delegar recursos a gerir através dos utilizadores no inquilino gestor.

Por exemplo, no exemplo acima descrito, o Inquilino A pode ser considerado como o inquilino prestador de serviços (o inquilino gerente) e o Inquilino B e Inquilino C podem ser considerados como os inquilinos do cliente.

Neste exemplo, os utilizadores do Tenant A com as permissões adequadas podem [ver e gerir os recursos delegados](../how-to/view-manage-customers.md) na página dos meus **clientes** do portal Azure. Da mesma forma, os utilizadores do Tenant B e do Tenant C com as permissões adequadas podem [ver e gerir os recursos que foram delegados](../how-to/view-manage-service-providers.md) ao Arrendatário A na página de prestadores de **serviços** do portal Azure.

## <a name="next-steps"></a>Passos seguintes

- Conheça [as experiências de gestão de inquilinos cruzados.](cross-tenant-management-experience.md)
- Saiba mais sobre a [Gestão de recursos delegados do Azure](azure-delegated-resource-management.md).