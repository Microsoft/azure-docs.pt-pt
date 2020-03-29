---
title: Registe a sua candidatura para utilizar o Diretório Ativo Azure [ ) Microsoft Docs
description: Escrito para o IT Pro, este artigo fornece orientações para integrar aplicações Azure com Diretório Ativo.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: article
ms.date: 10/30/2018
ms.author: mimart
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba54f8042c20a00f8d559ddce28e007a93afaace
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67108288"
---
# <a name="develop-line-of-business-apps-for-azure-active-directory"></a>Desenvolver aplicativos de linha de negócio para o Azure Ative Directory
Este guia fornece uma visão geral do desenvolvimento de aplicações de linha de negócio (LoB) para o Azure Ative Directory (AD). O público pretendido é ative directory/Office 365 administradores globais.

## <a name="overview"></a>Descrição geral
As aplicações de construção integradas com a Azure AD dão aos utilizadores da sua organização um único sign-on com o Office 365. Ter a aplicação em Azure AD dá-lhe controlo sobre a política de autenticação para a aplicação. Para saber mais sobre o Acesso Condicional e como proteger as aplicações com a autenticação de vários fatores (MFA) consulte as regras de [acesso de Configuração](../conditional-access/app-based-mfa.md).

Registe a sua candidatura para utilizar o Diretório Ativo Azure. Registar a aplicação significa que os seus desenvolvedores podem utilizar a AD Azure para autenticar os utilizadores e solicitar o acesso a recursos do utilizador, tais como e-mail, calendário e documentos.

Qualquer membro do seu diretório (não hóspedes) pode registar uma aplicação, também conhecida como criação de um objeto de *aplicação*.

O registo de uma aplicação permite que qualquer utilizador faça o seguinte:

* Obtenha uma identidade para a sua aplicação que a Azure AD reconhece
* Obtenha um ou mais segredos/chaves que a aplicação pode usar para autenticar-se a AD
* Marque a aplicação no portal Azure com um nome, logotipo, etc.
* Aplicar funcionalidades de autorização da Azure AD na sua aplicação, incluindo:

  * Controlo de Acesso Baseado em Funções (RBAC)
  * Diretório Ativo Azure como servidor de autorização oAuth (proteja uma API exposta pela aplicação)
* Declarar as permissões necessárias para que o pedido funcione como esperado, incluindo:

     - Permissões de aplicações (apenas administradores globais). Por exemplo: Adesão a outra aplicação da AD Azure ou a de filiação a funções relativas a um Recurso Azure, Grupo de Recursos ou Subscrição
     - Permissões delegadas (qualquer utilizador). Por exemplo: Azure AD, Sign-in e Read Profile

> [!NOTE]
> Por predefinição, qualquer membro pode registar uma aplicação. Para aprender a restringir as permissões para registar aplicações a membros específicos, consulte [como as aplicações são adicionadas à AD Azure](../develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).
>
>

Eis o que você, o administrador global, precisa fazer para ajudar os desenvolvedores a prepararem a sua aplicação para produção:

* Configure regras de acesso (política de acesso/MFA)
* Configure a app para exigir a atribuição do utilizador e atribuir utilizadores
* Suprimir a experiência de consentimento do utilizador padrão

## <a name="configure-access-rules"></a>Configure as regras de acesso
Configure as regras de acesso por aplicação às suas aplicações SaaS. Por exemplo, pode exigir MFA ou apenas permitir o acesso aos utilizadores em redes fidedignas. Os detalhes para tal estão disponíveis no documento Regras de [acesso configurantes.](../conditional-access/app-based-mfa.md)

## <a name="configure-the-app-to-require-user-assignment-and-assign-users"></a>Configure a app para exigir a atribuição do utilizador e atribuir utilizadores
Por padrão, os utilizadores podem aceder a aplicações sem serem atribuídos. No entanto, se a aplicação expor funções ou se pretender que a aplicação apareça no painel de acesso de um utilizador, deverá necessitar da atribuição do utilizador.

Se é assinante do Azure AD Premium ou da Enterprise Mobility Suite (EMS), recomendamos vivamente a utilização de grupos. Atribuir grupos à aplicação permite-lhe delegar a gestão de acesso em curso ao proprietário do grupo. Você pode criar o grupo ou pedir ao responsável da sua organização para criar o grupo usando o seu centro de gestão de grupo.

[Atribuir utilizadores e grupos a uma aplicação](methods-for-assigning-users-and-groups.md)  


## <a name="suppress-user-consent"></a>Suprimir o consentimento do utilizador
Por padrão, cada utilizador passa por uma experiência de consentimento para iniciar sessão. A experiência de consentimento, pedindo aos utilizadores que concedam permissões a uma aplicação, pode ser desconcertante para os utilizadores que não estão familiarizados com a tomada de tais decisões.

Para aplicações em que confia, pode simplificar a experiência do utilizador consentir com a aplicação em nome da sua organização.

Para obter mais informações sobre o consentimento do utilizador e a experiência de consentimento no Azure, consulte A Integração de [Aplicações com o Diretório Ativo Azure](../develop/quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="related-articles"></a>Artigos Relacionados
* [Permitir o acesso remoto seguro a aplicações no local com procuração de aplicação ad-ad azure](application-proxy.md)
* [Gerir o acesso a apps com AD Azure](what-is-access-management.md)

