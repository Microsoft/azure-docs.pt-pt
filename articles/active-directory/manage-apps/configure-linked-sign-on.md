---
title: Compreender o sinal de inscrição ligado no Azure Ative Directory
description: Compreenda o sinal de inscrição ligado no Diretório Ativo Azure.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/30/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: 67cb8d6bf6dd61fb5ba7aec70a79078cbe2b8b13
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91597836"
---
# <a name="understand-linked-sign-on"></a>Compreender o sinal de inscrição ligado

Na [série quickstart](view-applications-portal.md) sobre gestão de aplicações, aprendeu a usar a Azure AD como Fornecedor de Identidade (IdP) para uma aplicação. No guia quickstart, configura sSO baseado em SAML ou OIDC. Outra opção é **o Linked**. Este artigo entra em mais detalhes sobre a opção ligada.

A opção **Linked** permite configurar o local alvo quando um utilizador seleciona a aplicação no portal [My Apps](https://myapps.microsoft.com/) ou Office 365 da sua organização.

Alguns cenários comuns em que a opção de ligação é valiosa incluem:
- Adicione um link a uma aplicação web personalizada que atualmente utiliza a federação, como o Ative Directory Federation Services (AD FS).
- Adicione links profundos a páginas específicas do SharePoint ou a outras páginas web que apenas pretende aparecer nos Painéis de Acesso do utilizador.
- Adicione um link a uma aplicação que não requer autenticação. 
 
 A opção **Linked** não fornece funcionalidade de inscrição através de credenciais AZure AD. Mas, ainda pode utilizar algumas das outras funcionalidades das aplicações da **Enterprise.** Por exemplo, pode usar registos de auditoria e adicionar um logotipo personalizado e nome de aplicação.

## <a name="before-you-begin"></a>Antes de começar

Para aumentar rapidamente o conhecimento, caminhe pela [série quickstart](view-applications-portal.md) sobre gestão de aplicações. No arranque rápido, onde configurar um único sign-on, também encontrará a opção **Linked.** 

A opção **Linked** não fornece funcionalidade de inscrição através do Azure AD. A opção simplesmente define a localização para onde os utilizadores serão enviados quando selecionarem a aplicação nas [Minhas Apps](https://myapps.microsoft.com/) ou no launcher da aplicação Microsoft 365.

> [!IMPORTANT] 
> Existem alguns cenários em que a opção **de inscrição única** não estará na navegação para uma aplicação em **aplicações da Enterprise.** 
>
> Se a aplicação foi registada usando **registos de Aplicações,** então a capacidade de inscrição única é configurada para utilizar o OIDC OAuth por padrão. Neste caso, a opção **de inscrição única** não será apresentada na navegação ao abrigo das **aplicações da Enterprise.** Quando utiliza **as inscrições da App** para adicionar a sua aplicação personalizada, configura as opções no ficheiro manifesto. Para saber mais sobre o ficheiro manifesto, consulte o [manifesto da aplicação Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest). Para saber mais sobre os padrões SSO, consulte [autenticação e autorização utilizando a plataforma de identidade da Microsoft.](https://docs.microsoft.com/azure/active-directory/develop/authentication-vs-authorization#authentication-and-authorization-using-microsoft-identity-platform) 
>
> Outros cenários em que o único sinal de sessão estará ausente da navegação incluem quando uma aplicação é hospedada noutro inquilino ou se a sua conta não tiver as permissões necessárias (Administrador Global, Administrador de Aplicação **cloud,** Administrador de Aplicação ou proprietário do principal de serviço). As permissões também podem causar um cenário em que pode abrir **o single de sposição,** mas não será capaz de economizar. Para saber mais sobre as funções administrativas da Azure AD, consulte https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .

### <a name="configure-link"></a>Ligação de configuração

Para definir um link para uma aplicação, selecione **Linked** na página **de sign-on único.** Em seguida, introduza o link e **selecione Guardar**. Precisa de um lembrete de onde encontrar estas opções? Confira a [série quickstart](view-applications-portal.md).
 
Depois de configurar uma aplicação, atribua-lhe utilizadores e grupos. Quando atribuímos os utilizadores, pode controlar quando a aplicação aparece nas [Minhas Apps](https://myapps.microsoft.com/) ou no launcher da aplicação Microsoft 365.

## <a name="next-steps"></a>Passos seguintes

- [Atribuir utilizadores ou grupos à aplicação](methods-for-assigning-users-and-groups.md)
- [Configurar o provisionamento automático da conta de utilizador](../app-provisioning/configure-automatic-user-provisioning-portal.md)
