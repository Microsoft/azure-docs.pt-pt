---
title: Candidatura inesperada na minha lista de candidaturas Microsoft Docs
description: Como ver todas as aplicações no seu inquilino e entender como as candidaturas aparecem na sua lista de Todas as Aplicações ao abrigo de Aplicações Empresariais
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1cb4eeb52d0680695bda266ad1a563b2ef5ee02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "65781102"
---
# <a name="unexpected-application-in-my-applications-list"></a>Candidatura inesperada na minha lista de candidaturas

Este artigo ajuda-o a entender como as aplicações aparecem na sua lista **de Todas as Aplicações** no âmbito de **Aplicações Empresariais**. 

## <a name="how-to-see-all-applications-in-your-tenant"></a>Como ver todas as aplicações no seu inquilino

Para ver todas as aplicações no seu inquilino, precisa de utilizar o controlo **filter** para mostrar **todas as aplicações** na lista de Todas as **Aplicações.** Siga estes passos.

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministrador.**

2.  Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4.  clique **em Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative Diretório.

5.  clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

6.  clique na utilização do controlo **filtro** no topo da Lista de **Todas as Aplicações**.

7.  No painel **filter,** detete a opção **Mostrar** para **todas as aplicações.**

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>Por que aparece uma aplicação específica na minha lista de candidaturas?

Quando filtrada para **todas as aplicações,** a **Lista** de Todas **as Aplicações** mostra todos os objetos principais de serviço no seu inquilino. Os objetos principais de serviço podem aparecer nesta lista de várias formas:

1. Quando adicionar qualquer aplicação na galeria de aplicações, incluindo:

   1. **Aplicações da Galeria Azure AD** – Uma aplicação que foi pré-integrada para um único sign-on com a Azure AD

   2. **Aplicações** de procuração de aplicações – Uma aplicação em execução no seu ambiente no local que pretende fornecer um único sinal seguro para externamente

   3. **Aplicações desenvolvidas sob medida** – Uma aplicação que a sua organização deseja desenvolver na Plataforma de Desenvolvimento de Aplicações AD Azure, mas que pode ainda não existir

   4. **Aplicações não-galeria** – Traga as suas próprias aplicações! Qualquer link web que deseje, ou qualquer aplicação que torne um campo de username e password, suporte os protocolos SAML ou OpenID Connect ou suporte sCIM que deseje integrar para um único sign-on com a Azure AD.

2. Ao inscrever-se ou subscrever uma candidatura de<sup>3º</sup> partido integrada no Azure Ative Directory. Um exemplo é [Smartsheet](https://app.smartsheet.com/b/home) ou [DocuSign](https://www.docusign.net/member/MemberLogin.aspx).

3. Ao inscrever-se ou adicionar uma licença a um utilizador ou a um grupo a uma aplicação de primeira parte, como o [Microsoft Office 365](https://products.office.com/)

4. Ao adicionar um novo registo de candidatura, criando uma aplicação desenvolvida sob medida através do Registo de [Aplicações](https://docs.microsoft.com/azure/active-directory/active-directory-app-registration)

5. Ao adicionar um novo registo de candidatura, criando uma aplicação desenvolvida sob medida através do portal de registo de [aplicações V2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-app-registration)

6. Quando adiciona uma aplicação está a desenvolver utilizando [métodos de autenticação ASP.net](https://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) do Estúdio Visual ou [Serviços Conectados](https://blogs.msdn.com/b/visualstudio/archive/2014/11/19/connecting-to-cloud-services.aspx)

7. Quando cria um objeto principal de serviço utilizando o [Módulo PowerShell Da AD Azure](/powershell/azure/install-adv2?view=azureadps-2.0)

8. Quando [consentir com um pedido](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) como administrador para usar dados no seu inquilino

9. Quando um [utilizador consente com uma aplicação](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) para utilizar dados no seu inquilino

10. Quando permite determinados serviços que armazenam dados no seu inquilino. Um exemplo é o Reset password, que é modelado como um principal de serviço para armazenar a sua política de redefinição de palavra-passe de forma segura.

Para obter mais detalhes sobre como as aplicações são adicionadas ao seu diretório, leia [Como e por que as aplicações são adicionadas ao Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added).

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Quero remover a atribuição de um utilizador ou grupo específico a uma aplicação

Para remover uma atribuição de utilizador ou grupo a uma aplicação, siga os passos listados no [Remove um utilizador ou atribuição de grupo de uma aplicação empresarial no artigo do Diretório Ativo do Azure.](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal)

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Quero desativar todo o acesso a uma aplicação para cada utilizador

Para desativar todos os registos de utilizadores numa aplicação, siga os passos listados nos registos de [utilizador desativados para uma aplicação empresarial no artigo do Diretório Ativo do Azure.](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal)

## <a name="i-want-to-delete-an-application-entirely"></a>Quero apagar uma aplicação inteiramente

Para **eliminar uma aplicação,** siga estes passos:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministrador.**

2. Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4. clique **em Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative Diretório.

5. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e detetete a opção **Mostrar** para Todas **as Aplicações.**

6. Selecione a aplicação que pretende eliminar.

7. Assim que a aplicação estiver carregada, clique em **Eliminar** o ícone do painel de **visão geral** da aplicação superior.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Quero desativar todas as futuras operações de consentimento do utilizador em qualquer aplicação

Desativar o consentimento do utilizador para todo o seu diretório impede que os utilizadores finais consintam em qualquer aplicação. Os administradores ainda podem consentir em nome do utilizador. Para saber mais sobre o consentimento da aplicação e por que pode ou não querer consentir, leia compreensão do [consentimento do utilizador e da administração.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview)

Para **desativar todas as futuras operações**de consentimento do utilizador em todo o seu diretório, siga estes passos:

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2.  Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4.  clique em **Utilizadores e grupos** no menu de navegação.

5.  clique nas **definições do utilizador**.

6.  Desative todas as futuras operações de consentimento do utilizador, definindo os **Utilizadores que podem permitir que as aplicações acedam aos seus dados** para **Não** e clique no botão **Guardar.**

## <a name="next-steps"></a>Passos seguintes
[Gestão de Aplicações com Diretório Ativo Azure](what-is-application-management.md)
