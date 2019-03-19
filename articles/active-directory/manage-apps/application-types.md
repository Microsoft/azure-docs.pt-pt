---
title: Aplicação inesperada na minha lista de aplicativos | Documentos da Microsoft
description: Como ver todas as aplicações no seu inquilino e compreender como as aplicações são apresentadas na sua lista de todos os aplicativos em aplicações empresariais
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: 075a50802a05a9b8254ff6ab1e0a38f43baca970
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58105629"
---
# <a name="unexpected-application-in-my-applications-list"></a>Aplicação inesperada na minha lista de aplicações

Este artigo ajuda-o a compreender como as aplicações são apresentadas no seu **todos os aplicativos** lista sob **aplicações empresariais**. 

## <a name="how-to-see-all-applications-in-your-tenant"></a>Como ver todas as aplicações no seu inquilino

Para ver todos os aplicativos no seu inquilino, tem de utilizar o **filtro** controle para mostrar **todos os aplicativos** sob o **todos os aplicativos** lista. Siga estes passos.

1.  Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Coadministrador.**

2.  Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4.  Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5.  Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

6.  Clique na utilizar o **filtro** na parte superior do **todas as listas de aplicações**.

7.  Sobre o **filtro** painel, definir o **mostrar** a opção de **todas as aplicações.**

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>Por que é apresentada uma aplicação específica no meu todas as listas de aplicações?

Quando filtrado para **todos os aplicativos**, o **todos os aplicativos** **lista** mostra todos os objetos de Principal de serviço no seu inquilino. Objetos de Principal de serviço podem aparecer nesta lista de uma de várias formas:

1. Quando adicionar qualquer aplicação a partir da Galeria de aplicações, incluindo:

   1. **Aplicações de galeria do Azure AD** – uma aplicação que foi pré-integradas para início de sessão único com o Azure AD

   2. **Aplicações de Proxy de aplicação** – uma aplicação em execução no seu ambiente no local que deseja fornecer seguro início de sessão único para externamente

   3. **Personalizada aplicativos** – uma aplicação que sua organização deseja desenvolver na plataforma de desenvolvimento de aplicativo de AD do Azure, mas que talvez ainda não existe

   4. **Aplicações não à galeria** – traga os seus próprios aplicativos! Qualquer ligação web que desejar, qualquer aplicativo que processa um campo de nome de utilizador e palavra-passe, oferece suporte a protocolos SAML ou OpenID Connect ou suporta SCIM que deseja integrar para início de sessão único com o Azure AD.

2. Quando se inscrever ou iniciar sessão para um 3<sup>rd</sup> aplicação de terceiros integrada com o Azure Active Directory. Um exemplo é [Smartsheet](https://app.smartsheet.com/b/home) ou [DocuSign](https://www.docusign.net/member/MemberLogin.aspx).

3. Quando se inscrever ou a adição de uma licença a um utilizador ou grupo a uma primeira aplicação de terceiros, como [do Microsoft Office 365](https://products.office.com/)

4. Quando adiciona um novo registo de aplicação através da criação de uma aplicação personalizada desenvolvida utilizando o [Registro do aplicativo](https://docs.microsoft.com/azure/active-directory/active-directory-app-registration)

5. Quando adiciona um novo registo de aplicação através da criação de uma aplicação personalizada desenvolvida utilizando o [portal de registo de aplicação V2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-app-registration)

6. Quando adiciona uma aplicação esteja a desenvolver com o Visual Studio [métodos de autenticação do ASP.net](https://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) ou [serviços ligados](https://blogs.msdn.com/b/visualstudio/archive/2014/11/19/connecting-to-cloud-services.aspx)

7. Quando cria um objeto do principal de serviço com o [módulo Azure AD PowerShell](/powershell/azure/install-adv2?view=azureadps-2.0)

8. Quando [autorizar uma aplicação](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) como um administrador para utilizar dados no seu inquilino

9. Quando um [utilizador dá consentimento a uma aplicação](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) para utilizar dados no seu inquilino

10. Quando ativa determinados serviços que armazenam os dados no seu inquilino. Um exemplo é repor a palavra-passe, que é modelada como política de reposição de um principal de serviço para armazenar a palavra-passe com segurança.

Para obter mais detalhes sobre como as aplicações são adicionadas ao seu diretório, leia [como e por que os aplicativos são adicionados ao Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added).

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Quero remover a atribuição de um utilizador específico ou do grupo a uma aplicação

Para remover um utilizador ou a atribuição de grupo a uma aplicação, siga os passos apresentados no [remover uma atribuição de utilizador ou grupo a partir de uma aplicação empresarial no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) artigo.

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Quero desativar todos os acessos a uma aplicação para cada utilizador

Para desativar todos os utilizadores inícios de sessão a uma aplicação, siga os passos apresentados no [desativar utilizador-inícios de sessão de uma aplicação empresarial no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) artigo.

## <a name="i-want-to-delete-an-application-entirely"></a>Quero eliminar uma aplicação totalmente

Para **eliminar uma aplicação**, siga estes passos:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Coadministrador.**

2. Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione a aplicação que pretende eliminar.

7. Assim que o aplicativo é carregado, clique em **elimine** ícone da aplicação superior **descrição geral** painel.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Quero desativar todas as operações de consentimento de utilizador futuras para qualquer aplicação

Desativar o consentimento do utilizador para o diretório completo impedir que os utilizadores finais consentir qualquer aplicação. Os administradores ainda podem consentir em nome de utilizador. Para saber mais sobre o consentimento da aplicação e, por isso que pode ou não deve consentir, leia [consentimento de administrador e de utilizador de compreensão](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview).

Para **desativar todas as operações de consentimento de utilizador futuras no seu diretório inteiro**, siga estes passos:

1.  Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4.  Clique em **utilizadores e grupos** no menu de navegação.

5.  Clique em **definições de utilizador**.

6.  Desativar todas as operações de consentimento do utilizador futuras ao definir o **os utilizadores podem permitir que as aplicações acedam aos seus dados** alternar para **não** e clique nas **guardar** botão.

## <a name="next-steps"></a>Passos Seguintes
[Managing Applications with Azure Active Directory](what-is-application-management.md) (Gerir Aplicações com o Azure Active Directory)
