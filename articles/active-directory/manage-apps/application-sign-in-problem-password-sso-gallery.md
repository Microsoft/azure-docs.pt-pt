---
title: Problemas ao iniciar sessão numa aplicação da galeria do Azure AD configurada para a palavra-passe de início de sessão único | Documentos da Microsoft
description: Como resolver problemas com a aplicação da galeria do Azure AD configurada para a palavra-passe início de sessão único
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 195d4f6e30b2e12b12419c4aea3948c6268c9dbe
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58087572"
---
# <a name="problems-signing-in-to-an-azure-ad-gallery-application-configured-for-password-single-sign-on"></a>Problemas ao iniciar sessão numa aplicação da galeria do Azure AD configurada para a palavra-passe início de sessão único

O painel de acesso é um portal baseado na web que permite que um utilizador que tem uma conta profissional ou escolar no Azure Active Directory (Azure AD) para ver e iniciar aplicativos de baseados na nuvem que o administrador do Azure AD tem concederia a eles acesso a. Um utilizador que tem as edições do Azure AD também pode usar grupos self-service e capacidades de gestão de aplicações através do painel de acesso. O painel de acesso separado no portal do Azure e não requer que os utilizadores tenham uma subscrição do Azure.

Para utilizar com base em palavra-passe de início de sessão único (SSO) no painel de acesso, a extensão do painel de acesso tem de estar instalada no navegador do usuário. Esta extensão é transferida automaticamente quando um usuário seleciona uma aplicação que está configurada para SSO baseado em palavra-passe.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Requisitos de browser de reunião para o painel de acesso

O painel de acesso requer um browser que suporte a JavaScript e CSS ativou o. Para utilizar com base em palavra-passe de início de sessão único (SSO) no painel de acesso, a extensão do painel de acesso tem de estar instalada no navegador do usuário. Esta extensão é transferida automaticamente quando um usuário seleciona uma aplicação que está configurada para SSO baseado em palavra-passe.

Para SSO de palavra-passe, os navegadores do usuário final podem ser:

-   Internet Explorer 8, 9, 10, 11 - no Windows 7 ou posterior

-   Chrome – no Windows 7 ou posterior e no MacOS X ou posterior

-   Firefox 26.0 ou posterior – no Windows XP SP2 ou posterior e no Mac OS X 10.6 ou posterior

>[!NOTE]
>A extensão SSO baseado em palavra-passe fica disponível para o Microsoft Edge no Windows 10 quando extensões do navegador passam a ser suportadas para o Microsoft Edge.
>
>

## <a name="how-to-install-the-access-panel-browser-extension"></a>Como instalar a extensão de Browser do painel de acesso

Para instalar a extensão de Browser do painel de acesso, siga estes passos:

1.  Abra o [painel de acesso](https://myapps.microsoft.com) em um dos browsers suportados e inicie sessão como um **utilizador** no seu Azure AD.

2.  Clique num **aplicação SSO de palavra-passe** no painel de acesso.

3.  Na linha de comandos pedindo para instalar o software, selecione **instalar agora**.

4.  Com base no seu browser, direcionado para a ligação de transferência. **Adicionar** a extensão ao seu navegador.

5.  Se o browser pede-lhe, selecione a qualquer um **habilitar** ou **permitir** a extensão.

6.  Depois de instalada **reiniciar** a sessão do browser.

7.  Iniciar sessão no painel de acesso e veja se pode **inicie** seus aplicativos de SSO de palavra-passe

Também pode baixar a extensão para o Chrome e Firefox dos links diretos abaixo:

-   [Extensão de painel de acesso do Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extensão do painel de acesso do Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="setting-up-a-group-policy-for-internet-explorer"></a>Configurar uma política de grupo para o Internet Explorer

Pode configurar uma política de grupo que permitem-lhe instalar remotamente a extensão do painel de acesso para o Internet Explorer nos computadores dos seus utilizadores.

Os pré-requisitos incluem:

-   Configurou [serviços de domínio do Active Directory](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx), e associar máquinas dos seus utilizadores ao seu domínio.

-   Tem de ter a permissão de "Edit settings" para editar o objeto de política de grupo (GPO). Por predefinição, os membros dos grupos de segurança seguintes têm esta permissão: Os administradores do domínio, administradores da empresa e proprietários de criador de políticas de grupo. [Saiba mais](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Siga o tutorial [como implementar a extensão do painel de acesso para o Internet Explorer usando a diretiva de grupo](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy) para obter instruções passo a passo sobre como configurar a política de grupo e implementá-la aos utilizadores.

## <a name="troubleshoot-the-access-panel-in-internet-explorer"></a>Resolver problemas relacionados com o painel de acesso no Internet Explorer

Siga os [resolver problemas relacionados com a extensão do painel de acesso para o Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting) guia para o acesso, uma ferramenta de diagnóstico e instruções passo a passo sobre como configurar a extensão para o IE.

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Como configurar a palavra-passe início de sessão único para uma aplicação da galeria do Azure AD

Para configurar uma aplicação da galeria do Azure AD, que tem de:

-   Adicionar uma aplicação a partir da galeria do Azure AD

-   [Configurar a aplicação para a palavra-passe início de sessão único](#configure-the-application-for-password-single-sign-on)

-   [Atribuir utilizadores à aplicação](#assign-users-to-the-application)

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Adicionar uma aplicação a partir da galeria do Azure AD

Para adicionar uma aplicação da galeria do Azure AD, siga estes passos:

1.  Abra o [portal do Azure](https://portal.azure.com) e inicie sessão como um **Administrador Global** ou **coadministrador**

2.  Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4.  Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5.  Clique nas **Add** botão no canto superior direito a **aplicações empresariais** painel.

6.  Na **introduza um nome** caixa de texto da **adicionar a partir da galeria** secção, escreva o nome da aplicação.

7.  Selecione a aplicação que pretende configurar para início de sessão único.

8.  Antes de adicionar a aplicação, pode alterar o nome a partir da **nome** caixa de texto.

9.  Clique em **adicionar** botão, para adicionar a aplicação.

Após um curto período, poderá ver o painel de configuração do aplicativo.

### <a name="configure-the-application-for-password-single-sign-on"></a>Configurar a aplicação para a palavra-passe início de sessão único

Para configurar o início de sessão único para uma aplicação, siga estes passos:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Coadministrador.**

2. Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione a aplicação que pretende configurar o início de sessão único

7. Assim que o aplicativo é carregado, clique nas **início de sessão único** no menu de navegação do lado esquerdo da aplicação.

8. Selecione o modo **baseado em palavra-passe de início de sessão.**

9. Atribua utilizadores à aplicação.

10. Além disso, também pode fornecer as credenciais em nome do utilizador ao selecionar as linhas dos utilizadores e clicar em **as credenciais de atualização** e introduzindo o nome de utilizador e palavra-passe em nome dos utilizadores. Caso contrário, ser pedido aos utilizadores para introduzir as credenciais durante a inicialização propriamente ditas.

### <a name="assign-users-to-the-application"></a>Atribuir utilizadores à aplicação

Para atribuir diretamente um ou mais utilizadores a uma aplicação, siga estes passos:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2. Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione a aplicação que pretende atribuir um utilizador a partir da lista.

7. Assim que o aplicativo é carregado, clique em **utilizadores e grupos** no menu de navegação do lado esquerdo da aplicação.

8. Clique nas **Add** botão na parte superior do **utilizadores e grupos** lista para abrir o **adicionar atribuição** painel.

9. Clique nas **utilizadores e grupos** Seletor da **adicionar atribuição** painel.

10. Escreva o **nome completo** ou **endereço de e-mail** do utilizador estiver interessado em atribuir para o **procurar por nome ou endereço de e-mail** caixa de pesquisa.

11. Paire o rato sobre o **usuário** na lista para revelar uma **caixa de verificação**. Clique na caixa de verificação junto a fotografia do perfil do usuário ou a logótipo para adicionar o utilizador para o **selecionados** lista.

12. **Opcional:** Se quiser **adicionar mais de um usuário**, tipo em outro **nome completo** ou **endereço de e-mail** para o **procurar por nome ou endereço de e-mail** caixa de pesquisa e clique na caixa de verificação para adicionar este utilizador para o **selecionados** lista.

13. Quando tiver terminado de selecionar utilizadores, clique nas **selecione** botão para adicioná-los à lista de utilizadores e grupos que devem ser atribuídos à aplicação.

14. **Opcional:** clique a **selecionar função** Seletor no **adicionar atribuição** painel para selecionar uma função para atribuir aos utilizadores que selecionou.

15. Clique nas **atribuir** botão para atribuir a aplicação aos utilizadores selecionados.

Após um curto período, os utilizadores que selecionou ser capaz de iniciar estas aplicações no painel de acesso.

## <a name="if-these-troubleshoot-steps-dont-resolve-the-issue"></a>Se resolver estes problemas passos não resolverem o problema 
Abra um pedido de suporte com as seguintes informações se estiver disponível:

-   ID de correlação de erro

-   UPN (endereço de e-mail do utilizador)

-   TenantID

-   Tipo de navegador

-   Fuso horário e tempo/período de tempo durante o erro ocorre

-   Rastreios do fiddler

## <a name="next-steps"></a>Passos Seguintes
[Fornecer início de sessão único às suas aplicações com o Proxy de aplicações](application-proxy-configure-single-sign-on-with-kcd.md)
