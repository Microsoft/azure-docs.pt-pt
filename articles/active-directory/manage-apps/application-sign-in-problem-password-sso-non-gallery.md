---
title: Usar SSO (logon único) baseado em senha no painel de acesso | Microsoft Docs
description: Discute áreas problemáticas que fornecem orientação para solucionar problemas relacionados à entrada em aplicativos da galeria do Azure AD configurados para logon único com senha.
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
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: c020f3ab3ea7ed96b72d0490b717225d44c4fac1
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277750"
---
# <a name="problems-signing-in-to-an-azure-ad-gallery-application-configured-for-password-single-sign-on"></a>Problemas ao entrar em um aplicativo da galeria do Azure AD configurado para logon único com senha

O painel de acesso é um portal baseado na Web que permite que um usuário que tem uma conta corporativa ou de estudante no Azure Active Directory (Azure AD) exiba e inicie aplicativos baseados em nuvem aos quais o administrador do Azure AD concedeu acesso. Um usuário que tem as edições do Azure AD também pode usar o grupo de autoatendimento e os recursos de gerenciamento de aplicativo por meio do painel de acesso. O painel de acesso é separado da portal do Azure e não exige que os usuários tenham uma assinatura do Azure.

Para usar o SSO (logon único) baseado em senha no painel de acesso, a extensão do painel de acesso deve ser instalada no navegador do usuário. Essa extensão é baixada automaticamente quando um usuário seleciona um aplicativo configurado para SSO baseado em senha.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Requisitos de navegador de reunião para o painel de acesso

O painel de acesso requer um navegador com suporte para JavaScript e com CSS habilitado. Para usar o SSO (logon único) baseado em senha no painel de acesso, a extensão do painel de acesso deve ser instalada no navegador do usuário. Essa extensão é baixada automaticamente quando um usuário seleciona um aplicativo configurado para SSO baseado em senha.

Para o SSO baseado em senha, os navegadores do usuário final podem ser:

-   Internet Explorer 8, 9, 10, 11--no Windows 7 ou posterior

-   Chrome – no Windows 7 ou posterior e no MacOS X ou posterior

-   Firefox 26,0 ou posterior – no Windows XP SP2 ou posterior e no Mac OS X 10,6 ou posterior

>[!NOTE]
>A extensão de SSO baseada em senha torna-se disponível para o Microsoft Edge no Windows 10 quando as extensões de navegador têm suporte para o Microsoft Edge.
>
>

## <a name="how-to-install-the-access-panel-browser-extension"></a>Como instalar a extensão do navegador do painel de acesso

Para instalar a extensão do navegador do painel de acesso, siga as etapas abaixo:

1.  Abra o [painel de acesso](https://myapps.microsoft.com) em um dos navegadores com suporte e entre como um **usuário** no Azure AD.

2.  clique em um **aplicativo de SSO de senha** no painel de acesso.

3.  No prompt solicitando a instalação do software, selecione **instalar agora**.

4.  Com base em seu navegador, você será direcionado para o link de download. **Adicione** a extensão ao seu navegador.

5.  Se seu navegador perguntar, selecione para **habilitar** ou **permitir** a extensão.

6.  Depois de instalado  , reinicie a sessão do navegador.

7.  Entre no painel de acesso e veja se você pode **Iniciar** seus aplicativos de SSO de senha

Você também pode baixar a extensão para o Chrome e Firefox nos links diretos abaixo:

-   [Extensão do painel de acesso do Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extensão do painel de acesso do Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="setting-up-a-group-policy-for-internet-explorer"></a>Configurando uma política de grupo para o Internet Explorer

Você pode configurar uma política de grupo que permita que você instale remotamente a extensão do painel de acesso para o Internet Explorer nos computadores dos usuários.

Os pré-requisitos incluem:

-   Você configurou [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)e ingressou nos computadores dos usuários em seu domínio.

-   Você deve ter a permissão "Editar configurações" para editar o objeto de Política de Grupo (GPO). Por padrão, os membros dos seguintes grupos de segurança têm essa permissão: Administradores de domínio, administradores de empresa e proprietários de Política de Grupo criador. [Saiba mais](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Siga o tutorial [como implantar a extensão do painel de acesso para o Internet Explorer usando política de grupo](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy) para obter instruções passo a passo sobre como configurar a política de grupo e implantá-la para os usuários.

## <a name="troubleshoot-the-access-panel-in-internet-explorer"></a>Solucionar problemas do painel de acesso no Internet Explorer

Siga o guia [solucionar problemas da extensão do painel de acesso para o Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting) para acessar uma ferramenta de diagnóstico e instruções passo a passo sobre como configurar a extensão para o IE.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Como configurar o logon único com senha para um aplicativo inexistente na Galeria

Para configurar um aplicativo da galeria do Azure AD, você precisa:

-   [Adicionar um aplicativo inexistente na Galeria](#add-a-non-gallery-application)

-   [Configurar o aplicativo para logon único com senha](#configure-the-application-for-password-single-sign-on)

-   [Atribuir usuários ao aplicativo](#assign-users-to-the-application)

### <a name="add-a-non-gallery-application"></a>Adicionar um aplicativo inexistente na Galeria

Para adicionar um aplicativo da galeria do Azure AD, siga as etapas abaixo:

1.  Abra o [portal do Azure](https://portal.azure.com) e entre como um **administrador global** ou coadministrador

2.  Abra a **extensão Azure Active Directory** clicando em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4.  clique em **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.

5.  Clique no botão **Adicionar** no canto superior direito do painel **aplicativos empresariais** .

6.  clique em aplicativo inexistente na **Galeria.**

7.  Insira o nome do seu aplicativo na caixa de texto **nome** . Selecione **Adicionar.**

Após um curto período, você poderá ver o painel de configuração do aplicativo.

### <a name="configure-the-application-for-password-single-sign-on"></a>Configurar o aplicativo para logon único com senha

Para configurar o logon único para um aplicativo, siga as etapas abaixo:

1. Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global** ou coadministrador **.**

2. Abra a **extensão Azure Active Directory** clicando em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. clique em **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione o aplicativo para o qual você deseja configurar o logon único

7. Depois que o aplicativo for carregado, clique no **logon único** no menu de navegação esquerdo do aplicativo.

8. Selecione o modo de **logon baseado em senha.**

9. Insira a **URL de logon**. Essa é a URL em que os usuários inserem seu nome de usuário e senha para entrar. Verifique se os campos de entrada estão visíveis na URL.

10. Atribua usuários ao aplicativo.

11. Além disso, você também pode fornecer credenciais em nome do usuário selecionando as linhas dos usuários e clicando em **Atualizar credenciais** e inserindo o nome de usuário e a senha em nome dos usuários. Caso contrário, os usuários serão solicitados a inserir as próprias credenciais na inicialização.

### <a name="assign-users-to-the-application"></a>Atribuir utilizadores à aplicação

Para atribuir diretamente um ou mais utilizadores a uma aplicação, siga os passos abaixo:

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

Após um curto período, os usuários que você selecionou poderão iniciar esses aplicativos no painel de acesso.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Se essas etapas de solução de problemas não resolverem o problema

Abra um tíquete de suporte com as seguintes informações, se disponíveis:

-   ID do erro de correlação

-   UPN (endereço de email do usuário)

-   TenantID

-   Tipo de navegador

-   Fuso horário e hora/período durante o erro ocorre

-   Rastreamentos do Fiddler

## <a name="next-steps"></a>Passos Seguintes
[Fornecer início de sessão único às suas aplicações com o Proxy de aplicações](application-proxy-configure-single-sign-on-with-kcd.md)

