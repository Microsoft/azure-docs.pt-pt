---
title: Configurar o sign-on ligado no Azure Ative Directory
description: Configurar o sinal de inscrição ligado em Azure AD.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 07/30/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: 2269a8f7f58d35fee5e2ca30a77af5e8cba83678
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87459339"
---
# <a name="configure-linked-sign-on"></a>Configurar o início de sessão ligado

Na [série quickstart](view-applications-portal.md) sobre gestão de aplicações, aprendeu a usar a Azure AD como Fornecedor de Identidade (IdP) para uma aplicação. No guia quickstart, você configura SSO baseado em SAML. Outra opção é **o Linked**. Este artigo entra em mais detalhes sobre a opção ligada.

A opção **Linked** permite configurar o local alvo quando um utilizador seleciona a aplicação no portal [My Apps](https://myapplications.microsoft.com/) ou Office 365 da sua organização.

Alguns cenários comuns em que a opção de ligação é valiosa incluem:
- Adicione um link a uma aplicação web personalizada que atualmente utiliza a federação, como o Ative Directory Federation Services (AD FS).
- Adicione links profundos a páginas específicas do SharePoint ou a outras páginas web que apenas pretende aparecer nos Painéis de Acesso do utilizador.
- Adicione um link a uma aplicação que não requer autenticação. 
 
 A opção **Linked** não fornece funcionalidade de inscrição através de credenciais AZure AD. Mas, ainda pode utilizar algumas das outras funcionalidades das aplicações da **Enterprise.** Por exemplo, pode usar registos de auditoria e adicionar um logotipo personalizado e nome de aplicação.

## <a name="before-you-begin"></a>Before you begin

Para acelerar rapidamente, caminhe pela [série quickstart](view-applications-portal.md) sobre gestão de aplicações. No arranque rápido, onde configurar um único sign-on, também encontrará a opção **Linked.** 

A opção **Linked** não fornece funcionalidade de inscrição através do Azure AD. A opção simplesmente define a localização para onde os utilizadores serão enviados quando selecionarem a aplicação nas [Minhas Apps](https://myapplications.microsoft.com/) ou no launcher da aplicação Microsoft 365.

> [!IMPORTANT] 
> Existem alguns cenários em que a opção **de inscrição única** não estará na navegação para uma aplicação em **aplicações da Enterprise.** 
>
> Se a aplicação foi registada usando **registos de Aplicações,** então a capacidade de inscrição única é configurada para utilizar o OIDC OAuth por padrão. Neste caso, a opção **de inscrição única** não aparecerá na navegação ao abrigo das **aplicações da Enterprise.** Quando utiliza **as inscrições da App** para adicionar a sua aplicação personalizada, configura as opções no ficheiro manifesto. Para saber mais sobre o ficheiro manifesto, consulte o [manifesto da aplicação Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest). Para saber mais sobre os padrões SSO, consulte [autenticação e autorização utilizando a plataforma de identidade da Microsoft.](https://docs.microsoft.com/azure/active-directory/develop/authentication-vs-authorization#authentication-and-authorization-using-microsoft-identity-platform) 
>
> Outros cenários em que o único sinal de sessão estará ausente da navegação incluem quando uma aplicação é hospedada noutro inquilino ou se a sua conta não tiver as permissões necessárias (Administrador Global, Administrador de Aplicação **cloud,** Administrador de Aplicação ou proprietário do principal de serviço). As permissões também podem causar um cenário em que pode abrir **o single de sposição,** mas não será capaz de economizar. Para saber mais sobre as funções administrativas da Azure AD, consulte https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .

### <a name="configure-link"></a>Ligação de configuração

Para definir um link para uma aplicação, selecione **Linked** na página **de sign-on único.** Em seguida, introduza o link e **selecione Guardar**. Precisa de um lembrete de onde encontrar estas opções? Confira a [série quickstart](view-applications-portal.md).
 
Depois de configurar uma aplicação, atribua-lhe utilizadores e grupos. Quando atribuímos os utilizadores, pode controlar quando a aplicação aparece nas [Minhas Apps](https://myapplications.microsoft.com/) ou no launcher da aplicação Microsoft 365.

## <a name="next-steps"></a>Passos seguintes

- [Atribuir utilizadores ou grupos à aplicação](methods-for-assigning-users-and-groups.md)
- [Configurar o provisionamento automático da conta de utilizador](../app-provisioning/configure-automatic-user-provisioning-portal.md)
