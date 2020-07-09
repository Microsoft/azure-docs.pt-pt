---
title: Azure Lighthouse em cenários empresariais
description: As capacidades do Farol Azure podem ser usadas para simplificar a gestão de inquilinos cruzados dentro de uma empresa que usa vários inquilinos AD Azure.
ms.date: 07/06/2020
ms.topic: conceptual
ms.openlocfilehash: 9f9a7aa81772a1edda5fd1915918b547a3066455
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86114147"
---
# <a name="azure-lighthouse-in-enterprise-scenarios"></a>Azure Lighthouse em cenários empresariais

O cenário mais comum para [o Azure Lighthouse](../overview.md) é um prestador de serviços que gere recursos nos inquilinos do Azure Ative Directory (Azure AD) dos seus clientes. No entanto, as capacidades do Farol Azure também podem ser usadas para simplificar a gestão de inquilinos cruzados dentro de uma empresa que usa vários inquilinos AZure AD.

## <a name="single-vs-multiple-tenants"></a>Solteiro vs. múltiplos inquilinos

Para a maioria das organizações, a gestão é mais fácil com um único inquilino AZure AD. Ter todos os recursos dentro de um inquilino permite centralizar tarefas de gestão por utilizadores designados, grupos de utilizadores ou diretores de serviço dentro desse inquilino. Recomendamos a utilização de um inquilino para a sua organização sempre que possível.

Ao mesmo tempo, existem situações que podem exigir uma organização para manter vários inquilinos da AD Azure. Em alguns casos, esta pode ser uma situação temporária, uma vez que quando as aquisições ocorreram e uma estratégia de consolidação de inquilinos de longo prazo levará algum tempo a definir. Uma organização também pode precisar de manter múltiplos inquilinos numa base contínua (devido a subsidiárias totalmente independentes, requisitos geográficos ou legais, e assim por diante). Nos casos em que é necessária uma arquitetura multi-arrendatário, o Farol de Azure pode ser usado para centralizar e dinamizar operações de gestão. As assinaturas de vários inquilinos podem ser a bordo para [a gestão de recursos delegados da Azure,](azure-delegated-resource-management.md)permitindo aos utilizadores designados num inquilino gerente desempenhar [funções de gestão de inquilinos transversais de](cross-tenant-management-experience.md) forma centralizada e escalável.

## <a name="tenant-management-architecture"></a>Arquitetura de gestão de inquilinos

Ao centralizar as operações de gestão em vários inquilinos, você precisará determinar qual inquilino incluirá os utilizadores que realizam operações de gestão para os outros inquilinos. Por outras palavras, você precisará determinar qual inquilino será o inquilino gerente para outros inquilinos.

Por exemplo, digamos que a sua organização tem um único inquilino que chamaremos *de Inquilino A.* A sua organização adquire então dois inquilinos adicionais, *Inquilino B* e *Inquilino C,* e tem razões comerciais que exigem que os mantenha como inquilinos separados.

A sua organização quer usar as mesmas definições políticas, práticas de backup e processos de segurança em todos os inquilinos. Uma vez que já tem utilizadores (incluindo grupos de utilizadores e diretores de serviço) que são responsáveis pela realização destas tarefas dentro do Inquilino A, pode embarcar todas as subscrições dentro do Inquilino B e inquilino C para que esses mesmos utilizadores no Inquilino A possam executar essas tarefas.

![Utilizadores no Inquilino A gerir recursos no Inquilino B e Inquilino C](../media/enterprise-azure-lighthouse.jpg)

## <a name="security-and-access-considerations"></a>Considerações de segurança e acesso

Na maioria dos cenários empresariais, você vai querer delegar uma subscrição completa para O Farol Azure, embora também possa delegar apenas grupos de recursos específicos dentro de uma subscrição.

De qualquer forma, certifique-se [de seguir o princípio do menor privilégio ao definir quais os utilizadores que terão acesso aos recursos](recommended-security-practices.md#assign-permissions-to-groups-using-the-principle-of-least-privilege). Ao fazê-lo, os utilizadores apenas têm as permissões necessárias para executar as tarefas necessárias e reduz a possibilidade de erros inadvertidos.

O Farol Azure apenas fornece ligações lógicas entre um inquilino gerente e inquilinos geridos, em vez de dados ou recursos fisicamente em movimento. Além disso, o acesso vai sempre em apenas uma direção, desde o arrendatário gerente até aos inquilinos geridos.  Os utilizadores e grupos do arrendatário gerente devem continuar a utilizar a autenticação de vários fatores na realização de operações de gestão sobre recursos de inquilino geridos.

As empresas com seguranças de governação interna ou externa e de conformidade podem utilizar [registos de atividades Azure](../../azure-monitor/platform/platform-logs-overview.md) para satisfazer os seus requisitos de transparência. Quando os inquilinos da empresa estabeleceram relações de inquilinos de gestão e gestão, os utilizadores em cada inquilino podem monitorizar e ganhar visibilidade às ações tomadas pelos utilizadores do outro inquilino, visualizando a atividade registada.

## <a name="onboarding-process-considerations"></a>Considerações do processo de embarque

As subscrições (ou grupos de recursos dentro de uma subscrição) podem ser a bordo do Farol Azure, quer através da implementação de modelos do Gestor de Recursos Azure, quer através de ofertas de Serviços Geridos publicadas no Azure Marketplace, tanto privadas como publicamente.

Uma vez que os utilizadores empresariais normalmente poderão ter acesso direto aos inquilinos da empresa, e não há necessidade de comercializar ou promover uma oferta de gestão, é geralmente mais rápido e simples de implementar diretamente com modelos de Gestor de Recursos Azure. Enquanto nos referimos a prestadores de serviços e clientes na [orientação de embarque,](../how-to/onboard-customer.md)as empresas podem usar os mesmos processos.

Se preferir, os inquilinos dentro de uma empresa podem ser a bordo [publicando uma oferta de Serviços Geridos ao Azure Marketplace.](../how-to/publish-managed-services-offers.md) Para garantir que a oferta só está disponível para os inquilinos apropriados, certifique-se de que os seus planos estão marcados como privados. Com um plano privado, você pode fornecer os IDs de subscrição para cada inquilino que você planeja a bordo, e ninguém mais será capaz de obter a sua oferta.

## <a name="terminology-notes"></a>Notas de terminologia

Para a gestão inter-arrendatário dentro da empresa, as referências aos prestadores de serviços na documentação do Farol Azure podem ser entendidas para se aplicarem ao inquilino gerente dentro de uma empresa , ou seja, o inquilino que inclui os utilizadores que irão gerir recursos em outros inquilinos através da gestão de recursos delegados da Azure. Da mesma forma, as referências aos clientes podem ser entendidas para se aplicarem aos inquilinos que estão a delegar recursos a serem geridos através dos utilizadores do inquilino gerente.

Por exemplo, no exemplo acima descrito, o Inquilino A pode ser considerado como o inquilino prestador de serviços (o inquilino gerente) e o Inquilino B e Inquilino C podem ser considerados como os inquilinos do cliente.

Neste exemplo, os utilizadores do Inquilino A com as permissões adequadas podem [visualizar e gerir recursos delegados](../how-to/view-manage-customers.md) na página dos **Meus clientes** do portal Azure. Da mesma forma, os utilizadores do Inquilino B e inquilino C com as permissões adequadas podem [visualizar e gerir os recursos que foram delegados](../how-to/view-manage-service-providers.md) ao Inquilino A na página de prestadores de **serviços** do portal Azure.

## <a name="next-steps"></a>Próximos passos

- Conheça as [experiências de gestão de inquilinos cruzados.](cross-tenant-management-experience.md)
- Saiba mais sobre a [Gestão de recursos delegados do Azure](azure-delegated-resource-management.md).