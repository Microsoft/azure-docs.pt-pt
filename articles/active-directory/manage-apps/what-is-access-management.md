---
title: Gerir o acesso a apps utilizando o Azure AD Microsoft Docs
description: Descreve como o Azure Ative Directory permite que as organizações especifiquem as aplicações a que cada utilizador tem acesso.
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
ms.openlocfilehash: 489b15423add03d69070bc32057af97396a85309
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409071"
---
# <a name="managing-access-to-apps"></a>Gerir o acesso a apps

A gestão contínua do acesso, avaliação de utilização e reporte continuam a ser um desafio depois de uma aplicação ser integrada no sistema de identidade da sua organização. Em muitos casos, os Administradores de TI ou helpdesk têm de assumir um papel ativo contínuo na gestão do acesso às suas apps. Às vezes, a atribuição é realizada por uma equipa de TI geral ou divisional. Muitas vezes, a decisão de atribuição destina-se a ser delegada no decisor do negócio, exigindo a sua aprovação antes de a TI fazer a atribuição.  Outras organizações investem na integração com um sistema automatizado de gestão de identidade e acesso, como o Controlo de Acesso baseado em Funções (RBAC) ou o Controlo de Acesso Baseado em Atributos (ABAC). Tanto a integração como o desenvolvimento de regras tendem a ser especializados e dispendiosos. O acompanhamento ou a comunicação de qualquer uma das abordagens de gestão é um investimento separado, dispendioso e complexo.

## <a name="how-does-azure-active-directory-help"></a>Como ajuda o Azure Ative Directory?

A Azure AD apoia uma gestão extensiva do acesso a aplicações configuradas, permitindo às organizações alcançar facilmente as políticas de acesso adequadas que vão desde a atribuição automática, baseada em atributos (cenários ABAC ou RBAC) através de delegação e incluindo gestão de administradores. Com o Azure AD, você pode facilmente alcançar políticas complexas, combinando vários modelos de gestão para uma única aplicação e pode até mesmo reutilizar regras de gestão em aplicações com o mesmo público.

Com a AD Azure, o relatório de utilização e atribuição está totalmente integrado, permitindo aos administradores reportarfacilmente sobre o estado de atribuição, erros de atribuição e até mesmo uso.

### <a name="assigning-users-and-groups-to-an-app"></a>Atribuir utilizadores e grupos a uma app

A atribuição de candidaturas da Azure AD centra-se em dois modos de atribuição primários:

* **Atribuição individual** Um administrador de TI com permissões do Administrador Global de Diretório pode selecionar contas individuais de utilizador e conceder-lhes acesso à aplicação.

* **Atribuição baseada em grupo (requer Azure AD Premium P1 ou P2)** Um administrador de TI com permissões do Administrador Global de Diretório pode atribuir um grupo à aplicação. O acesso específico dos utilizadores é determinado se são membros do grupo no momento em que tentam aceder à aplicação. Ou seja, um administrador pode efetivamente criar uma regra de atribuição indicando que "qualquer membro atual do grupo designado tem acesso à aplicação". Utilizando esta opção de atribuição, os administradores podem beneficiar de qualquer uma das opções de gestão do grupo Azure AD, incluindo [grupos dinâmicos baseados em atributos,](../fundamentals/active-directory-groups-create-azure-portal.md)grupos de sistemas externos (por exemplo, no local Ative Directory ou Workday), ou grupos geridos por administradores ou auto-geridos por serviços. Um único grupo pode ser facilmente atribuído a várias aplicações, certificando-se de que as aplicações com afinidade de atribuição podem partilhar regras de atribuição, reduzindo a complexidade geral da gestão. Note que os membros do grupo aninhado não são apoiados para atribuição de candidaturas baseadas em grupo neste momento.

Utilizando estes dois modos de atribuição, os administradores podem alcançar qualquer abordagem desejável de gestão de atribuição.

### <a name="requiring-user-assignment-for-an-app"></a>Exigindo a atribuição do utilizador para uma aplicação

Com certos tipos de aplicações, tem a opção de exigir que [os utilizadores sejam atribuídos à aplicação.](assign-user-or-group-access-portal.md#configure-an-application-to-require-user-assignment) Ao fazê-lo, evita que todos as sessão sintetizam, exceto os utilizadores que designa explicitamente para a aplicação. Os seguintes tipos de aplicações suportam esta opção:

* Aplicações configuradas para inscrição única federada (SSO) com autenticação baseada em SAML
* Aplicações proxy de aplicação que usam pré-autenticação de diretório ativo Azure
* Aplicações construídas na plataforma de aplicação Azure AD que utilizam a Autenticação de Ligação OAuth 2.0 / OpenID depois de um utilizador ou administrador ter consentido com essa aplicação. Certos pedidos empresariais oferecem controlo adicional sobre quem está autorizado a assinar.

Quando a atribuição do utilizador não é *necessária*, os utilizadores não atribuídos não vêem a aplicação no seu painel de acesso my Apps, mas ainda podem iniciar o início da aplicação (também conhecida como sign-on iniciado por SP) ou podem utilizar o URL de Acesso ao **Utilizador** na página **'Propriedades'** da aplicação (também conhecida como sinal iniciado pelo IDP).

Para algumas aplicações, a opção de exigir a atribuição do utilizador não está disponível nas propriedades da aplicação. Nestes casos, pode utilizar o PowerShell para definir a propriedade appRoleAssignmentRequireRequired no principal do serviço.

### <a name="determining-the-user-experience-for-accessing-apps"></a>Determinar a experiência do utilizador para aceder a apps

A Azure AD fornece [várias formas personalizáveis de implementar aplicações](end-user-experiences.md) para utilizadores finais na sua organização:

* Painel de acesso Azure AD My Apps
* Lançador de aplicações office 365
* Inscrição direta para aplicações federadas (service-pr)
* Ligações avançadas para aplicações federadas, baseadas em palavras-passe ou existentes

Pode determinar se os utilizadores atribuídos a uma aplicação empresarial podem vê-la no painel de acesso e no lançador de aplicações do Office 365.

## <a name="example-complex-application-assignment-with-azure-ad"></a>Exemplo: Atribuição complexa de candidaturas com AD Azure
Considere um pedido como salesforce. Em muitas organizações, a Salesforce é usada principalmente pelas equipas de marketing e vendas. Muitas vezes, os membros da equipa de marketing têm acesso altamente privilegiado à Salesforce, enquanto os membros da equipa de vendas têm acesso limitado. Em muitos casos, uma vasta população de trabalhadores da informação restringiu o acesso à aplicação. As exceções a estas regras complicam as coisas. Muitas vezes é a prerrogativa das equipas de marketing ou de liderança de vendas conceder ao utilizador acesso ou alterar as suas funções independentemente destas regras genéricas.

Com a AD Azure, aplicações como a Salesforce podem ser pré-configuradas para um único sinal (SSO) e provisionamento automatizado. Uma vez configurada a aplicação, um Administrador pode tomar a ação única para criar e atribuir os grupos apropriados. Neste exemplo, um administrador poderia executar as seguintes atribuições:

* [Os grupos dinâmicos](../fundamentals/active-directory-groups-create-azure-portal.md) podem ser definidos para representar automaticamente todos os membros das equipas de marketing e vendas usando atributos como departamento ou papel:
  
  * Todos os membros de grupos de marketing seriam designados para o papel de "marketing" na Salesforce
  * Todos os membros de grupos de equipas de vendas seriam designados para o papel de "vendas" na Salesforce. Um novo refinamento poderia utilizar vários grupos que representam equipas de vendas regionais atribuídas a diferentes funções da Salesforce.

* Para permitir o mecanismo de exceção, poderia ser criado um grupo de self-service para cada função. Por exemplo, o grupo "Salesforce marketing exception" pode ser criado como um grupo de self-service. O grupo pode ser atribuído ao papel de marketing da Salesforce e a equipa de liderança de marketing pode ser feita como proprietária. Os membros da equipa de liderança de marketing podem adicionar ou remover os utilizadores, definir uma política de adesão, ou mesmo aprovar ou negar os pedidos de adesão de cada utilizador. Este mecanismo é suportado através de uma experiência adequada ao trabalhador da informação que não requer formação especializada para proprietários ou membros.

Neste caso, todos os utilizadores designados seriam automaticamente provisionados à Salesforce, uma vez que são adicionados a diferentes grupos, a sua atribuição de funções seria atualizada na Salesforce. Os utilizadores seriam capazes de descobrir e aceder ao Salesforce através do painel de acesso à aplicação da Microsoft, clientes web do Office, ou mesmo navegando na sua página de login organizacional salesforce. Os administradores seriam capazes de ver facilmente o estado de utilização e atribuição usando relatórios da AD Azure.

Os administradores podem empregar [o Azure AD Conditional Access](../active-directory-conditional-access-azure-portal.md) para definir políticas de acesso para funções específicas. Estas políticas podem incluir se o acesso é permitido fora do ambiente corporativo e até mesmo requisitos de Autenticação Multi-Factor ou dispositivo para obter acesso em vários casos.

## <a name="access-to-microsoft-applications"></a>Acesso a aplicações da Microsoft

As aplicações da Microsoft (como office 365 Exchange, SharePoint, Yammer, etc.) são atribuídas e geridas de forma um pouco diferente das aplicações SaaS de terceiros ou outras aplicações que integra com a Azure AD para um único sinal.

Existem três formas principais de um utilizador ter acesso a uma aplicação publicada pela Microsoft.

- Para aplicações no Office 365 ou outras suites pagas, os utilizadores têm acesso através da atribuição de **licenças** diretamente à sua conta de utilizador, ou através de um grupo que utiliza a nossa capacidade de atribuição de licenças baseada em grupo.
- Para aplicações que a Microsoft ou uma terceira parte publica livremente para qualquer pessoa usar, os utilizadores podem ter acesso através do consentimento do [utilizador](configure-user-consent.md). Isto significa que eles assinam a aplicação com a sua conta Azure AD Work ou School e permitem-lhe ter acesso a algum conjunto limitado de dados na sua conta.
- Para aplicações que a Microsoft ou uma terceira parte publica livremente para qualquer pessoa usar, os utilizadores também podem ter acesso através do [consentimento do administrador](manage-consent-requests.md). Isto significa que um administrador determinou que o pedido pode ser usado por todos na organização, pelo que assina mato no pedido com uma conta de Administrador Global e concede acesso a todos na organização.

Algumas aplicações combinam estes métodos. Por exemplo, certas aplicações da Microsoft fazem parte de uma subscrição do Office 365, mas ainda requerem consentimento.

Os utilizadores podem aceder às aplicações do Office 365 através dos seus portais do Office 365. Também pode mostrar ou ocultar aplicações do Office 365 no painel de acesso my Apps com o [Office 365 visibilidade alternado](hide-application-from-user-portal.md) nas **definições**de utilizador do seu diretório . 

Tal como acontece com as aplicações empresariais, pode [atribuir utilizadores](assign-user-or-group-access-portal.md) a determinadas aplicações da Microsoft através do portal Azure ou, caso a opção portal não esteja disponível, utilizando o PowerShell.

## <a name="next-steps"></a>Passos seguintes
* [Proteger aplicativos com acesso condicional](../active-directory-conditional-access-azure-portal.md)
* [Gestão de grupos de self-service/SSAA](../users-groups-roles/groups-self-service-management.md)
