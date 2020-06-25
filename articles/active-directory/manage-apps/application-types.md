---
title: Candidatura inesperada na minha lista de candidaturas Microsoft Docs
description: Como ver todas as aplicações no seu inquilino e entender como as aplicações aparecem na sua lista de Todas as Aplicações ao abrigo de Aplicações empresariais
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97eef1f331d8e6965e378d6a76cfa7a0c50feb9a
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85317467"
---
# <a name="unexpected-application-in-my-applications-list"></a>Aplicação inesperada na minha lista de candidaturas

Este artigo ajuda-o a entender como as aplicações aparecem na sua lista **de Todas as Aplicações** ao abrigo **de Aplicações empresariais.** 

## <a name="how-to-see-all-applications-in-your-tenant"></a>Como ver todas as aplicações no seu inquilino

Para ver todas as aplicações no seu inquilino, é necessário utilizar o controlo **Filter** para mostrar **todas as aplicações** na lista de Todas as **Aplicações.** Siga estes passos.

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministração.**

2.  Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4.  clique em **Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative.

5.  clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

6.  clique na utilização do controlo **do filtro** no topo da Lista de Todas **as Aplicações**.

7.  No painel **de filtro,** desa estale a opção **'Mostrar'** a **todas as aplicações.**

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>Por que uma aplicação específica aparece na minha lista de todos os pedidos?

Quando filtrado para **todas as aplicações,** a **Lista** de Todas as Aplicações mostra todos os **objetos** principais de serviço no seu inquilino. Os objetos principais de serviço podem aparecer nesta lista de várias formas:

1. Quando adicionar qualquer aplicação na galeria de aplicações, incluindo:

   1. **Azure AD Gallery Applications** – Uma aplicação pré-integrada para um único sign-on com Azure AD

   2. **Aplicações Proxy Applications** – Uma aplicação em execução no seu ambiente no local que pretende fornecer um único sinal seguro para externamente

   3. **Aplicações desenvolvidas sob medida** – Uma aplicação que a sua organização deseja desenvolver na Plataforma de Desenvolvimento de Aplicações AD AZure, mas que pode ainda não existir

   4. **Aplicações não-galerias** – Traga as suas próprias aplicações! Qualquer ligação web que pretenda, ou qualquer aplicação que torne um nome de utilizador e um campo de palavra-passe, suporte protocolos SAML ou OpenID Connect, ou suporte o SCIM que pretende integrar para um único sign-on com AZure AD.

2. Ao inscrever-se ou iniciar sessão numa aplicação de 3<sup>primeiras</sup> partes integrada com o Azure Ative Directory. Um exemplo é [Smartsheet](https://app.smartsheet.com/b/home) ou [DocuSign](https://www.docusign.net/member/MemberLogin.aspx).

3. Ao inscrever-se ou adicionar uma licença a um utilizador ou grupo a uma aplicação de primeira parte, como [o Microsoft Office 365](https://products.office.com/)

4. Quando adiciona um novo registo de candidaturas criando uma aplicação desenvolvida por medida utilizando o [Registo de Pedidos](https://docs.microsoft.com/azure/active-directory/active-directory-app-registration)

5. Quando adiciona um novo registo de candidaturas criando uma aplicação personalizada utilizando o [portal V2.0 Application Registration](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-app-registration)

6. Ao adicionar uma aplicação que está a desenvolver utilizando os métodos de [autenticação ASP.net](https://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) do Visual Studio ou [serviços conectados](https://devblogs.microsoft.com/visualstudio/connecting-to-cloud-services/)

7. Quando cria um objeto principal de serviço utilizando o [Módulo Azure AD PowerShell](/powershell/azure/install-adv2?view=azureadps-2.0)

8. Quando [consente num pedido](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) como administrador para utilizar dados no seu inquilino

9. Quando um [utilizador consente com uma aplicação](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) para utilizar dados no seu inquilino

10. Quando ativa determinados serviços que armazenam dados no seu inquilino. Um exemplo é o Reset da Palavra-Passe, que é modelado como um principal de serviço para armazenar a sua política de reset de palavra-passe de forma segura.

Para obter mais detalhes sobre como as aplicações são adicionadas ao seu diretório, leia [Como e por que as aplicações são adicionadas ao Azure AD.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added)

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Quero remover a atribuição de um utilizador ou grupo específico a uma aplicação

Para remover uma atribuição de utilizador ou grupo a uma aplicação, siga os passos listados na [atribuição de um utilizador ou grupo de uma aplicação da empresa no artigo do Azure Ative Directory.](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal)

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Quero desativar todo o acesso a uma aplicação para cada utilizador

Para desativar todas as insuposições do utilizador para uma aplicação, siga os [passos listados nas insusagens do utilizador de Disable para uma aplicação empresarial no artigo do Azure Ative Directory.](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal)

## <a name="i-want-to-delete-an-application-entirely"></a>Quero apagar uma aplicação inteiramente

Para **eliminar uma aplicação,** siga estes passos:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministração.**

2. Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4. clique em **Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative.

5. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e desate a opção **'Mostrar'** a **todas as aplicações.**

6. Selecione a aplicação que pretende eliminar.

7. Uma vez que a aplicação é carregada, clique em **Eliminar** o ícone do painel **de visão geral** da aplicação superior.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Quero desativar todas as futuras operações de consentimento do utilizador a qualquer aplicação

Desativar o consentimento do utilizador para todo o seu diretório impede que os utilizadores finais consintam em qualquer aplicação. Os administradores ainda podem consentir em nome do utilizador. Para saber mais sobre o consentimento da aplicação e por que pode ou não querer consentir, leia [o consentimento do utilizador e da administração](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview).

Para **desativar todas as futuras operações de consentimento do utilizador em todo o seu diretório,** siga estes passos:

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2.  Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4.  clique em **Utilizadores e grupos** no menu de navegação.

5.  clique nas **definições do Utilizador**.

6.  Desativar todas as futuras operações de consentimento do utilizador, definindo os **Utilizadores, pode permitir que as aplicações acedam aos seus dados** para **alternar** para No e clicar no botão **Guardar.**

## <a name="next-steps"></a>Passos seguintes
[Gestão de Aplicações com Diretório Ativo Azure](what-is-application-management.md)
