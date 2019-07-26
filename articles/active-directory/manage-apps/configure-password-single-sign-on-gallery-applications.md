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
ms.openlocfilehash: eb37fe247901b799a845ce75723a4a6b535cbb28
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68422579"
---
# <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Como configurar o logon único com senha para um aplicativo da galeria do Azure AD

Ao adicionar um aplicativo da Galeria de [aplicativos do Azure ad](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis), você tem a opção de como deseja que os usuários entrem nesse aplicativo. Você pode configurar essa opção a qualquer momento selecionando o item de navegação **logon único** em um aplicativo empresarial no [portal do Azure](https://portal.azure.com/).

Um dos métodos de logon único disponíveis para você é a opção de [logon único baseado em senha](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) . Essa é uma ótima maneira de começar a integrar aplicativos ao Azure AD rapidamente e permite que você:

-   Habilite **o logon único para seus usuários** armazenando e reproduzindo com segurança nomes de usuário e senhas para o aplicativo que você integrou ao Azure AD

-   **Suporte a aplicativos que exigem vários campos de entrada** para aplicativos que exigem mais do que apenas campos de nome de usuário e senha para entrar

-   **Personalize os rótulos** dos campos de entrada de nome de usuário e senha que os usuários veem no [painel de acesso do aplicativo](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) quando inserem suas credenciais

-   Permitir que **os usuários** forneçam seus próprios nomes de usuário e senhas para as contas de aplicativos existentes que estão digitando manualmente no [painel de acesso do aplicativo](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

-   Permitir que um **membro do grupo de negócios** especifique os nomes de usuários e senhas atribuídos a um usuário usando o recurso de [acesso de aplicativo](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) de autoatendimento

-   Permitir que um **administrador** especifique os nomes de usuários e senhas atribuídos a um usuário usando o recurso atualizar credenciais ao [atribuir um usuário a um aplicativo](#assign-a-user-to-an-application-directly)

-   Permitir que um **administrador** especifique o nome de usuário compartilhado ou a senha usada por um grupo de pessoas usando o recurso atualizar credenciais ao [atribuir um grupo a um aplicativo](#assign-an-application-to-a-group-directly)

A seção a seguir descreve como você pode habilitar o [logon único baseado em senha](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) para um aplicativo que já está na [Galeria de aplicativos do Azure ad](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="overview-of-steps-required"></a>Visão geral das etapas necessárias
Para configurar um aplicativo da galeria do Azure AD, você precisa:

-   [Adicionar um aplicativo da galeria do Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Configurar o aplicativo para logon único com senha](#configure-the-application-for-password-single-sign-on)

-   Atribuir o aplicativo a um usuário ou grupo

    -   [Atribuir um usuário diretamente a um aplicativo](#assign-a-user-to-an-application-directly)

    -   [Atribuir um aplicativo a um grupo diretamente](#assign-an-application-to-a-group-directly)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>Adicionar um aplicativo da galeria do Azure AD

Para adicionar um aplicativo da galeria do Azure AD, siga estas etapas:

1.  Abra o [portal do Azure](https://portal.azure.com) e entre como um **administrador global** ou coadministrador

2.  Abra a **extensão Azure Active Directory** clicando em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4.  clique em **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.

5.  Clique no botão **Adicionar** no canto superior direito do painel **aplicativos empresariais** .

6.  Na caixa de texto **Inserir um nome** na seção **Adicionar da Galeria** , digite o nome do aplicativo.

7.  Selecione o aplicativo que você deseja configurar para logon único.

8.  Antes de adicionar o aplicativo, você pode alterar seu nome na caixa de texto **nome** .

9.  Clique no botão **Adicionar** para adicionar o aplicativo.

Após um curto período, você poderá ver o painel de configuração do aplicativo.

## <a name="configure-the-application-for-password-single-sign-on"></a>Configurar o aplicativo para logon único com senha

Para configurar o logon único para um aplicativo, siga estas etapas:

1. Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global** ou coadministrador **.**

2. Abra a **extensão Azure Active Directory** clicando em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. clique em **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione o aplicativo para o qual você deseja configurar o logon único

7. Depois que o aplicativo for carregado, clique no **logon único** no menu de navegação esquerdo do aplicativo.

8. Selecione o modo de **logon baseado em senha.**

9. [Atribua usuários ao aplicativo](#assign-a-user-to-an-application-directly).

10. Além disso, você também pode fornecer credenciais em nome do usuário selecionando as linhas dos usuários e clicando em **Atualizar credenciais** e inserindo o nome de usuário e a senha em nome dos usuários. Caso contrário, os usuários serão solicitados a inserir as próprias credenciais na inicialização.

## <a name="assign-a-user-to-an-application-directly"></a>Atribuir um usuário diretamente a um aplicativo

Para atribuir um ou mais usuários a um aplicativo diretamente, siga estas etapas:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2. Abra a **extensão Azure Active Directory** clicando em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. clique em **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione a aplicação que pretende atribuir um utilizador a partir da lista.

7. Depois que o aplicativo for carregado, clique em **usuários e grupos** no menu de navegação esquerdo do aplicativo.

8. Clique nas **Add** botão na parte superior do **utilizadores e grupos** lista para abrir o **adicionar atribuição** painel.

9. Clique nas **utilizadores e grupos** Seletor da **adicionar atribuição** painel.

10. Escreva o **nome completo** ou **endereço de e-mail** do utilizador estiver interessado em atribuir para o **procurar por nome ou endereço de e-mail** caixa de pesquisa.

11. Paire o rato sobre o **usuário** na lista para revelar uma **caixa de verificação**. Clique na caixa de verificação junto a fotografia do perfil do usuário ou a logótipo para adicionar o utilizador para o **selecionados** lista.

12. **Adicional** Se você quiser **adicionar mais de um usuário**, digite outro **nome completo** ou **endereço de email** na caixa de pesquisa **Pesquisar por nome ou endereço de email** e clique na CheckBox para adicionar esse usuário à lista **selecionada** .

13. Quando tiver terminado de selecionar utilizadores, clique nas **selecione** botão para adicioná-los à lista de utilizadores e grupos que devem ser atribuídos à aplicação.

14. **Opcional:** clique a **selecionar função** Seletor no **adicionar atribuição** painel para selecionar uma função para atribuir aos utilizadores que selecionou.

15. Clique nas **atribuir** botão para atribuir a aplicação aos utilizadores selecionados.

## <a name="assign-an-application-to-a-group-directly"></a>Atribuir um aplicativo a um grupo diretamente

Para atribuir um ou mais grupos a um aplicativo diretamente, siga estas etapas:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2. Abra a **extensão Azure Active Directory** clicando em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. clique em **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione a aplicação que pretende atribuir um utilizador a partir da lista.

7. Depois que o aplicativo for carregado, clique em **usuários e grupos** no menu de navegação esquerdo do aplicativo.

8. Clique nas **Add** botão na parte superior do **utilizadores e grupos** lista para abrir o **adicionar atribuição** painel.

9. Clique nas **utilizadores e grupos** Seletor da **adicionar atribuição** painel.

10. Escreva o **nome do grupo completo** do grupo estiver interessado em atribuir para o **pesquisar por nome ou endereço de e-mail** caixa de pesquisa.

11. Paire o rato sobre o **grupo** na lista para revelar uma **caixa de verificação**. Clique na caixa de verificação junto a fotografia do perfil ou à logótipo para adicionar o utilizador para o grupo de **selecionados** lista.

12. **Adicional** Se você quiser **adicionar mais de um grupo**, digite outro **nome de grupo completo** na caixa de pesquisa **Pesquisar por nome ou endereço de email** e clique na CheckBox para adicionar esse grupo à lista **selecionada** .

13. Quando tiver terminado de selecionar os grupos, clique nas **selecione** botão para adicioná-los à lista de utilizadores e grupos que devem ser atribuídos à aplicação.

14. **Opcional:** clique a **selecionar função** Seletor no **adicionar atribuição** painel para selecionar uma função para atribuir aos grupos que selecionou.

15. Clique nas **atribuir** botão para atribuir a aplicação aos grupos selecionados.

Após um curto período, os usuários que você selecionou poderão iniciar esses aplicativos no painel de acesso.

## <a name="next-steps"></a>Passos seguintes
[Fornecer início de sessão único às suas aplicações com o Proxy de aplicações](application-proxy-configure-single-sign-on-with-kcd.md)
