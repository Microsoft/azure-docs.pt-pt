---
title: Gerenciando o acesso a aplicativos usando o Azure AD | Microsoft Docs
description: Descreve como Azure Active Directory permite que as organizações especifiquem os aplicativos aos quais cada usuário tem acesso.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99c7947b6469f64f2ea05b2290305710db1ee796
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68477114"
---
# <a name="managing-access-to-apps"></a>Gerenciando o acesso a aplicativos
O gerenciamento de acesso contínuo, a avaliação de uso e os relatórios continuam a ser um desafio depois que um aplicativo é integrado ao sistema de identidade da sua organização. Em muitos casos, os administradores de ti ou o helpdesk precisam fazer uma função ativa contínua no gerenciamento de acesso aos seus aplicativos. Às vezes, a atribuição é executada por uma equipe de ti geral ou de divisões. Muitas vezes, a decisão de atribuição deve ser delegada ao tomador de decisões de negócios, exigindo sua aprovação antes de fazer a atribuição.  Outras organizações investem em integração com um sistema de gerenciamento de acesso e identidade automatizado existente, como RBAC (controle de acesso baseado em função) ou ABAC (controle de acesso baseado em atributo). A integração e o desenvolvimento de regras tendem a ser especializados e caros. O monitoramento ou relatório sobre qualquer abordagem de gerenciamento é seu próprio investimento separado, dispendioso e complexo.

## <a name="how-does-azure-active-directory-help"></a>Como Azure Active Directory ajuda?
 O Azure AD dá suporte ao gerenciamento de acesso extensivo para aplicativos configurados, permitindo que as organizações alcancem facilmente as políticas de acesso corretas, desde a atribuição automática, baseada em atributo (cenários ABAC ou RBAC) por meio de delegação e incluindo gerenciamento de administradores. Com o Azure AD, você pode facilmente obter políticas complexas, combinando vários modelos de gerenciamento para um único aplicativo e pode até mesmo reutilizar regras de gerenciamento em aplicativos com o mesmo público.

* [Adicionando aplicativos novos ou existentes](add-gallery-app.md)

  A atribuição de aplicativo do Azure AD enfoca dois modos de atribuição principais:

* **Atribuição individual** Um administrador de ti com permissões de administrador global de diretório pode selecionar contas de usuário individuais e conceder a elas acesso ao aplicativo.
* **Atribuição baseada em grupo (somente Azure ad pago)** Um administrador de ti com permissões de administrador global de diretório pode atribuir um grupo ao aplicativo. O acesso de usuários específicos é determinado pelo fato de eles serem membros do grupo no momento em que tentam acessar o aplicativo. Em outras palavras, um administrador pode efetivamente criar uma regra de atribuição informando "qualquer membro atual do grupo atribuído tem acesso ao aplicativo". Usando essa opção de atribuição, os administradores podem se beneficiar de qualquer uma das opções de gerenciamento de grupo do Azure AD, incluindo [grupos dinâmicos baseados em atributo](../fundamentals/active-directory-groups-create-azure-portal.md), grupos de sistemas externos (por exemplo, Active Directory local ou workday) ou Grupos gerenciados por autoatendimento ou gerenciados pelo administrador. Um único grupo pode ser facilmente atribuído a vários aplicativos, certificando-se de que os aplicativos com afinidade de atribuição possam compartilhar regras de atribuição, reduzindo a complexidade geral do gerenciamento. Observe que as associações de grupo aninhadas não têm suporte para atribuição baseada em grupo para aplicativos no momento.

Usando esses dois modos de atribuição, os administradores podem obter qualquer abordagem de gerenciamento de atribuição desejável.

Com o Azure AD, os relatórios de uso e atribuição são totalmente integrados, permitindo que os administradores relatem facilmente o estado de atribuição, erros de atribuição e até mesmo o uso.

## <a name="complex-application-assignment-with-azure-ad"></a>Atribuição de aplicativo complexo com o Azure AD
Considere um aplicativo como o Salesforce. Em muitas organizações, o Salesforce é usado principalmente pelas equipes de marketing e vendas. Muitas vezes, os membros da equipe de marketing têm acesso altamente privilegiado ao Salesforce, enquanto os membros da equipe de vendas têm acesso limitado. Em muitos casos, uma população ampla de operadores de informações restringiu o acesso ao aplicativo. As exceções a essas regras complicam as coisas. Geralmente, as equipes de liderança de marketing ou vendas do prerrogativa concedem a um usuário acesso ou alteram suas funções independentemente dessas regras genéricas.

Com o Azure AD, aplicativos como o Salesforce podem ser pré-configurados para SSO (logon único) e provisionamento automatizado. Depois que o aplicativo é configurado, um administrador pode executar a ação única para criar e atribuir os grupos apropriados. Neste exemplo, um administrador pode executar as seguintes atribuições:

* [Grupos dinâmicos](../fundamentals/active-directory-groups-create-azure-portal.md) podem ser definidos para representar automaticamente todos os membros das equipes de marketing e vendas usando atributos como departamento ou função:
  
  * Todos os membros de grupos de marketing seriam atribuídos à função de "marketing" no Salesforce
  * Todos os membros de grupos de equipe de vendas seriam atribuídos à função de "vendas" no Salesforce. Um refinamento adicional poderia usar vários grupos que representam equipes de vendas regionais atribuídas a diferentes funções do Salesforce.
* Para habilitar o mecanismo de exceção, um grupo de autoatendimento pode ser criado para cada função. Por exemplo, o grupo "exceção de marketing do Salesforce" pode ser criado como um grupo de autoatendimento. O grupo pode ser atribuído à função de marketing do Salesforce e a equipe de liderança de marketing pode tornar-se proprietário. Os membros da equipe de liderança de marketing podem adicionar ou remover usuários, definir uma política de junção ou até mesmo aprovar ou negar as solicitações de usuários individuais para ingressar. Esse mecanismo tem suporte por meio de uma experiência apropriada do operador de informações que não requer treinamento especializado para proprietários ou membros.

Nesse caso, todos os usuários atribuídos seriam provisionados automaticamente para o Salesforce, pois eles são adicionados a diferentes grupos em que sua atribuição de função seria atualizada no Salesforce. Os usuários poderão descobrir e acessar o Salesforce por meio do painel de acesso do aplicativo da Microsoft, clientes do Office Web ou até mesmo navegando até a página de logon do Salesforce organizacional. Os administradores poderão exibir facilmente o status de uso e atribuição usando os relatórios do Azure AD.

Os administradores podem empregar o [acesso condicional do Azure ad](../active-directory-conditional-access-azure-portal.md) para definir políticas de acesso para funções específicas. Essas políticas podem incluir se o acesso é permitido fora do ambiente corporativo e até mesmo os requisitos de autenticação multifator ou dispositivo para obter acesso em vários casos.

## <a name="next-steps"></a>Passos Seguintes
* [Protegendo aplicativos com acesso condicional](../active-directory-conditional-access-azure-portal.md)
* [Gerenciamento de grupo de autoatendimento/SSAA](../users-groups-roles/groups-self-service-management.md)
