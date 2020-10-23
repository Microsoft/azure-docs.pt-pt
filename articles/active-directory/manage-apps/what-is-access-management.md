---
title: Gerir o acesso a apps usando Azure AD
description: Descreve como o Azure Ative Directory permite que as organizações especifiquem as aplicações a que cada utilizador tem acesso.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/16/2017
ms.author: kenwith
ms.openlocfilehash: c73a5ccd9e16fb5efd9fefca6253701f942c3202
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92367793"
---
# <a name="managing-access-to-apps"></a>Gerir o acesso a apps

A gestão contínua de acessos, a avaliação de utilização e os relatórios continuam a ser um desafio depois de uma aplicação ser integrada no sistema de identidade da sua organização. Em muitos casos, os administradores de TI ou helpdesk têm de assumir um papel ativo contínuo na gestão do acesso às suas apps. Às vezes, a atribuição é realizada por uma equipa de TI geral ou divisional. Muitas vezes, a decisão de atribuição destina-se a ser delegada no decisor do negócio, exigindo a sua aprovação antes de a TI fazer a atribuição.  Outras organizações investem na integração com um sistema de gestão de identidade e acesso automatizado existente, como Role-Based Access Control (RBAC) ou Attribute-Based Access Control (ABAC). Tanto a integração como o desenvolvimento de regras tendem a ser especializados e dispendiosos. O acompanhamento ou a comunicação de qualquer uma das abordagens de gestão é o seu próprio investimento separado, dispendioso e complexo.

## <a name="how-does-azure-active-directory-help"></a>Como é que o Azure Ative Directory ajuda?

A Azure AD suporta uma gestão extensiva do acesso para aplicações configuradas, permitindo que as organizações consigam facilmente as políticas de acesso certas que vão desde a atribuição automática, baseada em atributos (cenários ABAC ou RBAC) através de delegação e incluindo a gestão de administradores. Com o Azure AD, você pode facilmente alcançar políticas complexas, combinando vários modelos de gestão para uma única aplicação e pode até reutilizar regras de gestão entre aplicações com o mesmo público.

Com o Azure AD, o uso e o relatório de atribuição estão totalmente integrados, permitindo aos administradores reportar facilmente sobre o estado de atribuição, erros de atribuição e até mesmo o uso.

### <a name="assigning-users-and-groups-to-an-app"></a>Atribuir utilizadores e grupos a uma aplicação

A atribuição de candidaturas da AD da AZure centra-se em dois modos de atribuição primários:

* **Atribuição individual** Um administrador de TI com permissões de Administrador Global pode selecionar contas individuais de utilizador e conceder-lhes acesso à aplicação.

* **Atribuição baseada em grupo (requer Azure AD Premium P1 ou P2)** Um administrador de TI com permissões de administrador global pode atribuir um grupo à aplicação. O acesso específico dos utilizadores é determinado se são membros do grupo no momento em que tentam aceder à aplicação. Por outras palavras, um administrador pode efetivamente criar uma regra de atribuição indicando que "qualquer membro atual do grupo designado tem acesso à aplicação". Utilizando esta opção de atribuição, os administradores podem beneficiar de qualquer uma das opções de gestão do grupo AZure AD, incluindo [grupos dinâmicos baseados em atributos,](../fundamentals/active-directory-groups-create-azure-portal.md)grupos de sistema externos (por exemplo, no local Ative Directy ou Workday), ou grupos geridos por administrador ou geridos por si próprios. Um único grupo pode ser facilmente designado para várias aplicações, certificando-se de que aplicações com afinidade de atribuição podem partilhar regras de atribuição, reduzindo a complexidade geral da gestão. Note que os membros do grupo aninhados não são suportados para atribuição baseada em grupo para aplicações neste momento.

Utilizando estes dois modos de atribuição, os administradores podem alcançar qualquer abordagem desejável de gestão de atribuição.

### <a name="requiring-user-assignment-for-an-app"></a>Exigindo a atribuição do utilizador para uma aplicação

Com certos tipos de aplicações, tem a opção de exigir que [os utilizadores sejam designados para a aplicação.](assign-user-or-group-access-portal.md#configure-an-application-to-require-user-assignment) Ao fazê-lo, impede que todos entrem em ações, exceto os utilizadores que atribuem explicitamente à aplicação. Os seguintes tipos de aplicações suportam esta opção:

* Aplicações configuradas para o sign-on único federado (SSO) com autenticação baseada em SAML
* Aplicações Proxy que usam pré-autenticação do Diretório Ativo Azure
* Aplicações construídas na plataforma de aplicação AD Azure que utilizam AAuth 2.0 / OpenID Connect Authentication depois de um utilizador ou administrador ter consentido com essa aplicação. Algumas aplicações da empresa oferecem controlo adicional sobre quem é autorizado a entrar.

Quando a atribuição do utilizador não é *necessária,* os utilizadores não designados não vêem a aplicação nas suas Aplicações, mas ainda podem iniciar sessão na própria aplicação (também conhecida como sessão iniciada pelo SP) ou podem utilizar o **URL de Acesso** ao Utilizador na página **Propriedades** da aplicação (também conhecida como placa iniciada pelo IDP).

Para algumas aplicações, a opção de exigir a atribuição do utilizador não está disponível nas propriedades da aplicação. Nestes casos, pode utilizar o PowerShell para definir a propriedade appRoleAssignmentRequired no principal serviço.

### <a name="determining-the-user-experience-for-accessing-apps"></a>Determinar a experiência do utilizador para aceder a apps

A Azure AD fornece [várias formas personalizáveis de implementar aplicações](end-user-experiences.md) para utilizadores finais na sua organização:

* Azure AD My Apps
* Lançador de aplicações Microsoft 365
* Acesso direto a aplicações federadas (service-pr)
* Ligações avançadas para aplicações federadas, baseadas em palavras-passe ou existentes

Pode determinar se os utilizadores designados para uma aplicação empresarial podem vê-la nas Minhas Apps e no launcher da aplicação Microsoft 365.

## <a name="example-complex-application-assignment-with-azure-ad"></a>Exemplo: Atribuição complexa de candidaturas com Azure AD
Considere uma aplicação como a Salesforce. Em muitas organizações, a Salesforce é usada principalmente pelas equipas de marketing e vendas. Muitas vezes, os membros da equipa de marketing têm acesso altamente privilegiado à Salesforce, enquanto os membros da equipa de vendas têm acesso limitado. Em muitos casos, uma vasta população de trabalhadores da informação restringiu o acesso à aplicação. Exceções a estas regras complicam as coisas. Muitas vezes é prerrogativa das equipas de marketing ou de liderança de vendas conceder a um utilizador acesso ou alterar as suas funções independentemente destas regras genéricas.

Com o Azure AD, aplicações como a Salesforce podem ser pré-configuradas para um único sign-on (SSO) e provisão automatizada. Uma vez configurado o pedido, um Administrador pode tomar a ação única para criar e atribuir os grupos apropriados. Neste exemplo, um administrador poderia executar as seguintes atribuições:

* [Os grupos dinâmicos](../fundamentals/active-directory-groups-create-azure-portal.md) podem ser definidos para representar automaticamente todos os membros das equipas de marketing e vendas utilizando atributos como departamento ou papel:
  
  * Todos os membros de grupos de marketing seriam designados para o papel de "marketing" na Salesforce
  * Todos os membros de grupos de equipas de vendas seriam designados para o papel de "vendas" na Salesforce. Um novo refinamento poderia utilizar vários grupos que representam equipas de vendas regionais atribuídas a diferentes funções da Salesforce.

* Para permitir o mecanismo de exceção, um grupo de self-service poderia ser criado para cada papel. Por exemplo, o grupo "Salesforce marketing exception" pode ser criado como um grupo de self-service. O grupo pode ser designado para o papel de marketing da Salesforce e a equipa de liderança de marketing pode ser nomeada proprietária. Os membros da equipa de liderança de marketing poderiam adicionar ou remover utilizadores, definir uma política de união, ou mesmo aprovar ou negar os pedidos de aderião de utilizadores individuais. Este mecanismo é suportado através de uma experiência adequada do trabalhador da informação que não requer formação especializada para proprietários ou membros.

Neste caso, todos os utilizadores designados seriam automaticamente a provisionados à Salesforce, uma vez que são adicionados a diferentes grupos a sua atribuição de funções seria atualizada na Salesforce. Os utilizadores seriam capazes de descobrir e aceder à Salesforce através das Minhas Apps, clientes web do Office, ou mesmo navegando para a sua página de login organizacional Salesforce. Os administradores seriam capazes de visualizar facilmente o estado de utilização e atribuição utilizando relatórios AZure AD.

Os administradores podem empregar [acesso condicional Azure AD](../conditional-access/concept-conditional-access-users-groups.md) para definir políticas de acesso para funções específicas. Estas políticas podem incluir se o acesso é permitido fora do ambiente corporativo e até mesmo requisitos de autenticação multi-factor ou dispositivo para obter acesso em vários casos.

## <a name="access-to-microsoft-applications"></a>Acesso às aplicações da Microsoft

As aplicações da Microsoft (como Exchange, SharePoint, Yammer, etc.) são atribuídas e geridas de forma um pouco diferente das aplicações SaaS de terceiros ou outras aplicações que integra com a AZure AD para um único sinal.

Existem três formas principais de um utilizador ter acesso a uma aplicação publicada pela Microsoft.

- Para aplicações no Microsoft 365 ou outras suites pagas, os utilizadores têm acesso através de **uma atribuição de licenças** diretamente à sua conta de utilizador, ou através de um grupo que utiliza a nossa capacidade de atribuição de licenças baseada no grupo.
- Para aplicações que a Microsoft ou uma terceira parte publica livremente para qualquer pessoa utilizar, os utilizadores podem ter acesso através do [consentimento do utilizador.](configure-user-consent.md) Isto significa que eles assinam a aplicação com a sua conta Azure AD Work ou Escola e permitem-lhe ter acesso a algum conjunto limitado de dados na sua conta.
- Para aplicações que a Microsoft ou uma terceira parte publica livremente para qualquer pessoa usar, os utilizadores também podem ter acesso através do [consentimento do administrador](manage-consent-requests.md). Isto significa que um administrador determinou que o pedido pode ser usado por todos na organização, por isso eles assinam o pedido com uma conta de Administrador Global e concedem acesso a todos na organização.

Algumas aplicações combinam estes métodos. Por exemplo, certas aplicações da Microsoft fazem parte de uma subscrição do Microsoft 365, mas ainda requerem consentimento.

Os utilizadores podem aceder às aplicações da Microsoft 365 através dos seus portais Office 365. Também pode apresentar ou ocultar aplicações microsoft 365 nas Minhas Aplicações com a [visibilidade do Office 365 a alternar](hide-application-from-user-portal.md) nas definições do utilizador do seu **diretório**. 

Tal como acontece com as aplicações empresariais, é [possível atribuir utilizadores](assign-user-or-group-access-portal.md) a determinadas aplicações da Microsoft através do portal Azure ou, se a opção do portal não estiver disponível, utilizando o PowerShell.

## <a name="next-steps"></a>Passos seguintes
* [Proteger aplicativos com acesso condicional](../conditional-access/concept-conditional-access-cloud-apps.md)
* [Gestão de grupos de self-service/SSAA](../enterprise-users/groups-self-service-management.md)
