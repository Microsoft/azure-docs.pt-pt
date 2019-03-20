---
title: Gerir o acesso a aplicações com o Azure AD | Documentos da Microsoft
description: Descreve como o Azure Active Directory permite às organizações especificar as aplicações a que cada utilizador tem acesso.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2017
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70513675d09a663c65c6f5b3e18059467a8ba388
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58109329"
---
# <a name="managing-access-to-apps"></a>Gerir o acesso a aplicações
Gestão de acesso contínuo, avaliação de utilização e relatórios continuam a ser um desafio depois de uma aplicação é integrada ao sistema de identidade da sua organização. Em muitos casos, os administradores de TI ou suporte técnico tem de realizar uma função ativa em curso no gerenciamento de acesso às suas aplicações. Às vezes, a atribuição é realizada por uma equipa de TI geral ou divisão. Muitas vezes, a decisão de atribuição se destina a ser designado como o tomador de decisões de negócios, que requerem a sua aprovação antes de IT facilita a atribuição.  Outras organizações investem na integração com um automatizada identidades e acessos sistema de gerenciamento existente, como o controlo de acesso baseado em funções (RBAC) ou o controlo de acesso baseado em atributo (ABAC). A integração e o desenvolvimento de regra tendem a ser dispendiosos e especializados. Monitorização ou relatórios sobre qualquer uma das abordagens de gerenciamento são o seu próprio investimento separado, dispendioso e complexo.

## <a name="how-does-azure-active-directory-help"></a>Como o Azure Active Directory ajuda a?
 O Azure AD suporta a gestão de acesso abrangente para aplicações configuradas, que permite às organizações poderiam ser obtidas facilmente as políticas de acesso correto, desde a atribuição automática, baseadas em atributos (cenários de ABAC ou RBAC) por meio de delegação e incluindo gestão de administrador. Com o Azure AD, pode facilmente obter políticas complexas, combinar vários modelos de gestão para um único aplicativo e pode até reutilizar as regras de gestão em todos os aplicativos com os mesmo públicos.

* [A adicionar novas ou aplicações existentes](configure-single-sign-on-portal.md)

  Atribuição de aplicações do Azure AD se concentra em dois modos de atribuição principal:

* **Atribuição individual** administrador de TI um com permissões de Administrador Global do diretório pode selecionar as contas de utilizador individuais e conceder-lhes acesso à aplicação.
* **Atribuição baseada em grupo (paga apenas do Azure AD)** administrador de TI um com permissões de Administrador Global do diretório pode atribuir um grupo à aplicação. Acesso de utilizadores específicos é determinado pelo serem membros do grupo no momento que tentam aceder à aplicação. Em outras palavras, um administrador pode, efetivamente, criar uma regra de atribuição que diz "qualquer membro atual do grupo atribuído tem acesso à aplicação". Utilizar esta opção de atribuição, os administradores podem se beneficiar qualquer uma das opções de gestão de grupo do Azure AD, incluindo [grupos dinâmicos baseados em atributo](../fundamentals/active-directory-groups-create-azure-portal.md), grupos de sistema externo (por exemplo, no local do Active Directory ou Workday), ou grupos de administrador-gerida ou self-service. Um único grupo pode ser facilmente atribuído a várias aplicações, tornando-se de que os aplicativos com afinidade de atribuição podem partilhar as regras de atribuição, reduzindo a complexidade de gestão geral. Tenha em atenção que as associações a grupos aninhados não são suportadas para atribuição de grupo para aplicações neste momento.

Usando esses modos de atribuição de dois, os administradores podem obter qualquer abordagem de gestão de atribuição desejável.

Com o Azure AD, utilização e a atribuição de relatórios está totalmente integrado, permitindo que os administradores facilmente um relatório sobre o estado de atribuição, erros de atribuição e da utilização até mesmo.

## <a name="complex-application-assignment-with-azure-ad"></a>Atribuição de aplicações complexas com o Azure AD
Considere um aplicativo como o Salesforce. Em muitas organizações, o Salesforce é usado principalmente pelas equipes de marketing e vendas. Muitas vezes, os membros da equipe de marketing tem altamente acesso privilegiado ao Salesforce, enquanto os membros da equipe de vendas tem acesso limitado. Em muitos casos, uma vasta população de operadores de informações tem acesso restrito à aplicação. Exceções a essas regras complicam as coisas. Geralmente é prerogative das equipes de liderança de marketing ou vendas para conceder um acesso de utilizador ou alterar as suas funções, independentemente destas regras genéricas.

Com o Azure AD, os aplicativos como o Salesforce podem de ser previamente configurados para início de sessão único (SSO) e o aprovisionamento automatizado. Assim que a aplicação está configurada, um administrador pode executar a ação única para criar e atribuir grupos adequados. Neste exemplo, um administrador pode executar as atribuições de seguintes:

* [Grupos dinâmicos](../fundamentals/active-directory-groups-create-azure-portal.md) podem ser definidos para representar automaticamente todos os membros das equipes de marketing e vendas, usando atributos, como o departamento ou função:
  
  * Todos os membros de grupos de marketing seriam atribuídos à função de "marketing" no Salesforce
  * Todos os membros da equipa de vendas grupos seriam atribuídos à função de "venda", no Salesforce. Um refinamento adicional poderia usar vários grupos que representam as equipas de vendas regionais atribuídas a diferentes funções do Salesforce.
* Para ativar o mecanismo de exceção, poderia ser criado um grupo de self-service para cada função. Por exemplo, o grupo de "Exceção de marketing de Salesforce" pode ser criado como um grupo de gestão personalizada. O grupo pode ser atribuído à função de marketing do Salesforce e a equipe de liderança de marketing pode ser feita proprietário. Membros da equipe de liderança do marketing poderiam adicionar ou remover utilizadores, definir uma política de associação, ou até mesmo aprovar ou negar pedidos de utilizadores individuais para associar. Esse mecanismo é oferecido por meio de uma experiência adequado dos operadores de informações que não necessita de treinamento especializado para os proprietários ou membros.

Neste caso, todos os utilizadores atribuídos poderiam ser aprovisionados automaticamente para o Salesforce, à medida que são adicionados a grupos diferentes, que seria possível atualizar a respetiva atribuição de função no Salesforce. Os utilizadores seria capazes de detetar e aceder ao Salesforce, através do painel de acesso de aplicações Microsoft, clientes do Office web, ou até mesmo ao navegar para a página de início de sessão do Salesforce organizacional. Os administradores conseguirão ver facilmente o estado de utilização e a atribuição com relatórios do Azure AD.

Os administradores podem ser empregadas [acesso condicional do Azure AD](../active-directory-conditional-access-azure-portal.md) para definir políticas de acesso para funções específicas. Estas políticas podem incluir se o acesso é permitido fora do ambiente Corporativo e até mesmo os requisitos multi-factor Authentication ou dispositivo para obter acesso em vários casos.

## <a name="next-steps"></a>Passos Seguintes
* [Proteger aplicações com acesso condicional](../active-directory-conditional-access-azure-portal.md)
* [Gestão/SSAA de grupos self-service](../users-groups-roles/groups-self-service-management.md)
