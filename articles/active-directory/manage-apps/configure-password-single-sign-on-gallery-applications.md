---
title: Como configurar o logon único com senha para um aplicativo da galeria do Azure AD | Microsoft Docs
description: Como configurar um aplicativo para logon único baseado em senha de segurança quando ele já estiver listado na Galeria de aplicativos do Azure AD
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
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: 9fb33c4110a590539c85364885da9a27853f6bd0
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70146893"
---
# <a name="configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Configurar o logon único com senha para um aplicativo da galeria do Azure AD

Ao adicionar um aplicativo da Galeria de [aplicativos Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis), você pode escolher como deseja que os usuários entrem nesse aplicativo. Você pode configurar essa opção a qualquer momento selecionando **logon único** em um aplicativo empresarial no [portal do Azure](https://portal.azure.com/).

Uma das opções de SSO (logon único) disponíveis é [logon único baseado em senha](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis). Essa é uma ótima maneira de começar a integrar rapidamente aplicativos ao Azure AD. Ele permite o seguinte:

-   Armazena e repete com segurança nomes de domínio e senhas para o aplicativo que você integrou ao Azure AD

-   Dá suporte para aplicativos que exigem vários campos de entrada além dos campos nome de usuário e senha

-   Permite que você personalize os rótulos dos campos de nome de usuário e senha que os usuários veem no [painel de acesso do aplicativo](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) quando inserem suas credenciais

-   Permite que os usuários forneçam seus próprios nomes de usuário e senhas para todas as contas de aplicativos existentes que eles inserem manualmente no [painel de acesso do aplicativo](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

-   Permite que um membro do grupo de negócios use o recurso de [acesso de aplicativo](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) de autoatendimento para especificar os nomes de usuário e senhas atribuídos a um utilizador

-   Permite que um administrador especifique os nomes de usuários e senhas atribuídos a um usuário usando o recurso atualizar credenciais ao [atribuir um usuário a um aplicativo](#assign-a-user-to-an-application-directly)

-   Permite que um administrador use o recurso atualizar credenciais para especificar o nome de usuário compartilhado ou a senha para um grupo de pessoas ao [atribuir um grupo a um aplicativo](#assign-an-application-to-a-group-directly)

A seção a seguir descreve como você pode habilitar o [logon único baseado em senha](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) para um aplicativo que já está na [Galeria de aplicativos do Azure ad](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="overview-of-required-steps"></a>Visão geral das etapas necessárias
Para configurar um aplicativo da galeria do Azure AD, você precisa:

-   [Adicionar um aplicativo da galeria do Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Configurar o aplicativo para logon único com senha](#configure-the-application-for-password-single-sign-on)

-   Atribuir o aplicativo a um usuário ou a um grupo:

    -   [Atribuir um usuário diretamente a um aplicativo](#assign-a-user-to-an-application-directly)

    -   [Atribuir um aplicativo a um grupo diretamente](#assign-an-application-to-a-group-directly)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>Adicionar um aplicativo da galeria do Azure AD

Para adicionar um aplicativo da galeria do Azure AD, siga estas etapas:

1.  Abra o [portal do Azure](https://portal.azure.com)e entre como um **administrador global** ou coadministrador.

2.  Abra a **extensão Azure Active Directory** selecionando **todos os serviços** na parte superior do menu à esquerda.

3.  Insira **Azure Active Directory** na caixa de pesquisa e, em seguida, selecione o **Azure Active Directory** item.

4.  Selecione **aplicativos empresariais** no menu do Azure ad à esquerda.

5.  Selecione o botão **Adicionar** no canto superior direito do painel **aplicativos empresariais** .

6.  Na caixa **Inserir um nome** na seção **Adicionar da Galeria** , insira o nome do aplicativo.

7.  Selecione o aplicativo que você deseja configurar para logon único.

8.  Antes de adicionar o aplicativo, você pode alterar seu nome na caixa **nome** .

9.  Selecione **Adicionar** para adicionar o aplicativo.

Após um curto período, você poderá ver o painel de configuração do aplicativo.

## <a name="configure-the-application-for-password-single-sign-on"></a>Configurar o aplicativo para logon único com senha

Para configurar o logon único para um aplicativo, siga estas etapas:

1. Abra o [portal do Azure](https://portal.azure.com/) e entre como um **administrador global** ou coadministrador.

2. Abra a **extensão Azure Active Directory** selecionando **todos os serviços** na parte superior do menu à esquerda.

3. Insira **Azure Active Directory** na caixa de pesquisa e, em seguida, selecione o **Azure Active Directory** item.

4. Selecione **aplicativos empresariais** no menu Azure Active Directory à esquerda.

5. Selecione **todos os aplicativos** para exibir uma lista de todos os seus aplicativos.

   Se você não vir o aplicativo desejado aqui, use o controle de **filtro** na parte superior da **lista todos os aplicativos**e defina a opção **Mostrar** como **todos os aplicativos**.

6. Selecione o aplicativo que você deseja configurar para logon único.

7. Depois que o aplicativo for carregado, selecione **logon único** no menu do aplicativo à esquerda.

8. Selecione o modo de **logon baseado em senha** .

9. [Atribua usuários ao aplicativo](#assign-a-user-to-an-application-directly).

10. Você também pode fornecer credenciais em nome dos usuários, selecionando a linha de um usuário, selecionando **Atualizar credenciais**e, em seguida, inserindo o nome e a senha. Caso contrário, os usuários serão solicitados a inserir suas credenciais quando iniciarem o aplicativo.

## <a name="assign-a-user-to-an-application-directly"></a>Atribuir um usuário diretamente a um aplicativo

Para atribuir um ou mais usuários a um aplicativo diretamente, siga estas etapas:

1. Abra o [portal do Azure](https://portal.azure.com/) e entre como um **administrador global**.

2. Abra a **extensão Azure Active Directory** selecionando **todos os serviços** na parte superior do menu à esquerda.

3. Insira **Azure Active Directory** na caixa de pesquisa e, em seguida, selecione o **Azure Active Directory** item.

4. Selecione **aplicativos empresariais** no menu Azure Active Directory à esquerda.

5. Selecione **todos os aplicativos** para exibir uma lista de todos os seus aplicativos.

   Se você não vir o aplicativo desejado aqui, use o controle de **filtro** na parte superior da **lista todos os aplicativos**e defina a opção **Mostrar** como **todos os aplicativos**.

6. Selecione o aplicativo ao qual você deseja atribuir um usuário.

7. Depois que o aplicativo for carregado, selecione **usuários e grupos** no menu do aplicativo à esquerda.

8. Selecione o botão **Adicionar** na parte superior da lista **usuários e grupos** para abrir o painel **Adicionar atribuição** .

9. Selecione **usuários e grupos** no painel **Adicionar atribuição** .

10. Insira o nome completo ou o endereço de email do usuário na caixa de pesquisa **Pesquisar por nome ou endereço de email** .

11. Passe o mouse sobre o usuário na lista e marque a caixa de seleção ao lado da foto ou do logotipo do perfil do usuário para adicioná-los à lista **selecionada** .

12. Opcional: Se você quiser adicionar mais de um usuário, insira outro nome completo ou endereço de email na caixa **Pesquisar por nome ou endereço de email** e marque a caixa de seleção para esse usuário para adicioná-los à lista **selecionada** .

13. Quando você terminar de selecionar usuários, use o botão **selecionar** para adicioná-los à lista de usuários e grupos a serem atribuídos ao aplicativo.

14. Opcional: Use o comando **selecionar função** no painel **Adicionar atribuição** para selecionar uma função a ser atribuída aos usuários que você selecionou.

15. Selecione **atribuir** para atribuir o aplicativo aos usuários selecionados.

## <a name="assign-an-application-to-a-group-directly"></a>Atribuir um aplicativo a um grupo diretamente

Para atribuir um ou mais grupos a um aplicativo diretamente, siga estas etapas:

1. Abra o [portal do Azure](https://portal.azure.com/) e entre como um **administrador global**.

2. Abra a **extensão Azure Active Directory** selecionando **todos os serviços** na parte superior do menu à esquerda.

3. Insira **Azure Active Directory** na caixa de pesquisa e, em seguida, selecione o **Azure Active Directory** item.

4. Selecione **aplicativos empresariais** no menu do Azure ad à esquerda.

5. Selecione **todos os aplicativos** para exibir uma lista de todos os seus aplicativos.

   Se você não vir o aplicativo desejado aqui, use o controle de **filtro** na parte superior da **lista todos os aplicativos** e defina a opção **Mostrar** como **todos os aplicativos**.

6. Selecione o aplicativo ao qual você deseja atribuir um usuário.

7. Depois que o aplicativo for carregado, selecione **usuários e grupos** no menu do aplicativo à esquerda.

8. Selecione o botão **Adicionar** na parte superior da lista **usuários e grupos** para abrir o painel **Adicionar atribuição** .

9. Selecione **usuários e grupos** no painel **Adicionar atribuição** .

10. Insira o nome completo do grupo que você deseja atribuir na caixa de pesquisa **Pesquisar por nome ou endereço de email** .

11. Passe o mouse sobre o **grupo** na lista e marque a caixa de seleção ao lado da foto ou do logotipo do perfil do grupo para adicionar o grupo à lista **selecionada** .

12. Opcional: Se você quiser adicionar mais de um grupo, insira outro nome de grupo completo na caixa de pesquisa **Pesquisar por nome ou endereço de email** e, em seguida, marque a caixa de seleção correspondente para adicionar esse grupo à lista **selecionada** .

13. Quando terminar de selecionar grupos, use o botão **selecionar** para adicioná-los à lista de usuários e grupos a serem atribuídos ao aplicativo.

14. Opcional: Use o comando **selecionar função** no painel **Adicionar atribuição** para selecionar uma função a ser atribuída aos grupos selecionados.

15. Selecione **atribuir** para atribuir o aplicativo aos grupos selecionados.

Após um curto período, os usuários que você selecionou devem ser capazes de iniciar esses aplicativos no painel de acesso.

## <a name="next-steps"></a>Passos Seguintes
[Forneça logon único para seus aplicativos por meio do proxy de aplicativo](application-proxy-configure-single-sign-on-with-kcd.md).
