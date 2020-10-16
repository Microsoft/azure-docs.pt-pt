---
title: Registe a sua candidatura para utilizar o Azure Ative Directory ! Microsoft Docs
description: Escrito para o IT Pro, este artigo fornece orientações para a integração de aplicações Azure com Diretório Ativo.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b24bd190eac9cb6c29ceab6eb597d8d1c48f2ff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90605095"
---
# <a name="develop-line-of-business-apps-for-azure-active-directory"></a>Desenvolver aplicativos de linha de negócios para o Azure Ative Directory
Este guia fornece uma visão geral do desenvolvimento de aplicações de linha de negócios (LoB) para o Azure Ative Directory (AD). O público pretendido é o Ative Directory/Microsoft 365 administradores globais.

## <a name="overview"></a>Descrição geral
As aplicações de construção integradas com Azure AD dão aos utilizadores da sua organização um único sign-on com o Microsoft 365. Ter a aplicação no Azure AD dá-lhe controlo sobre a política de autenticação para a aplicação. Para saber mais sobre o Acesso Condicional e como proteger aplicações com autenticação multi-factor (MFA) consulte [regras de acesso configuradas.](../conditional-access/app-based-mfa.md)

Registe a sua candidatura para utilizar o Azure Ative Directory. Registar a aplicação significa que os seus desenvolvedores podem usar a Azure AD para autenticar os utilizadores e solicitar o acesso aos recursos do utilizador, tais como e-mail, calendário e documentos.

Qualquer membro do seu diretório (não convidados) pode registar uma aplicação, também conhecida como *criação de um objeto de aplicação.*

O registo de uma aplicação permite a qualquer utilizador fazer o seguinte:

* Obtenha uma identidade para a sua aplicação que a AZure AD reconhece
* Obtenha um ou mais segredos/chaves que a aplicação pode usar para autenticar-se a AD
* Marque a aplicação no portal Azure com um nome personalizado, logotipo, etc.
* Aplique recursos de autorização AZure AD na sua aplicação, incluindo:

  * Controlo de Acesso Baseado em Funções (RBAC)
  * Azure Ative Directory como servidor de autorização oAuth (proteja uma API exposta pela aplicação)
* Declarar as permissões necessárias para que o pedido funcione como esperado, incluindo:

     - Permissões de aplicativos (apenas administradores globais). Por exemplo: Membro de função em outra aplicação AD Azure ou membro de função relativamente a um Azure Resource, Resource Group ou Subscrição
     - Permissões delegadas (qualquer utilizador). Por exemplo: Azure AD, Iniciar sessão e Ler Perfil

> [!NOTE]
> Por predefinição, qualquer membro pode registar uma candidatura. Para saber como restringir as permissões de registo de pedidos a membros [específicos, consulte como as aplicações são adicionadas ao Azure AD.](../develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance)
>
>

Eis o que você, o administrador global, precisa de fazer para ajudar os desenvolvedores a prepararem a sua aplicação para a produção:

* Configure regras de acesso (política de acesso/MFA)
* Configure a app para exigir a atribuição do utilizador e atribuir aos utilizadores
* Suprimir a experiência de consentimento do utilizador predefinido

## <a name="configure-access-rules"></a>Configurar regras de acesso
Configure as regras de acesso por aplicação às suas aplicações SaaS. Por exemplo, pode exigir MFA ou apenas permitir o acesso aos utilizadores em redes fidedignas. Os detalhes para tal estão disponíveis no documento [que configura as regras de acesso.](../conditional-access/app-based-mfa.md)

## <a name="configure-the-app-to-require-user-assignment-and-assign-users"></a>Configure a app para exigir a atribuição do utilizador e atribuir aos utilizadores
Por predefinição, os utilizadores podem aceder às aplicações sem serem atribuídos. No entanto, se a aplicação expor funções ou se pretender que a aplicação apareça nas Minhas Apps de um utilizador, deverá requerer a atribuição do utilizador.

Se você é um assinante do Azure AD Premium ou do Enterprise Mobility Suite (EMS), recomendamos vivamente a utilização de grupos. A atribuição de grupos à aplicação permite-lhe delegar a gestão de acesso em curso ao proprietário do grupo. Pode criar o grupo ou pedir ao responsável da sua organização que crie o grupo utilizando o seu centro de gestão de grupos.

[Atribuir utilizadores e grupos a uma aplicação](methods-for-assigning-users-and-groups.md)  


## <a name="suppress-user-consent"></a>Suprimir o consentimento do utilizador
Por predefinição, cada utilizador passa por uma experiência de consentimento para iniciar sação. A experiência de consentimento, pedindo aos utilizadores que concedam permissões a uma aplicação, pode ser desconcertante para os utilizadores que não estão familiarizados com tais decisões.

Para aplicações em que confia, pode simplificar a experiência do utilizador, consentindo com a aplicação em nome da sua organização.

Para obter mais informações sobre o consentimento do utilizador e a experiência de consentimento no Azure, consulte [Integrar Aplicações com o Azure Ative Directory](../develop/quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="related-articles"></a>Artigos Relacionados
* [Permitir o acesso remoto seguro a aplicações no local com Proxy de aplicação AD AZure](application-proxy.md)
* [Gerir o acesso a apps com Azure AD](what-is-access-management.md)

