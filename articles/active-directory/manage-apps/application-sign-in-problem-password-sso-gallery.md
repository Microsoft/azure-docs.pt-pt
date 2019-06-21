---
title: Problema ao iniciar sessão a uma aplicação da galeria do Azure AD configurada para SSO de palavra-passe | Documentos da Microsoft
description: Como resolver problemas com um aplicativo de galeria do Azure AD que está configurado para a palavra-passe início de sessão único.
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
ms.openlocfilehash: 742df882fb64e09ff63ef2eceb5514ca070dc227
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190331"
---
# <a name="sign-in-problems-with-an-azure-ad-gallery-app-configured-for-sso"></a>Problemas de início de sessão com uma aplicação da galeria do Azure AD configurada para SSO

Painel de acesso é um portal baseado na web. Permite que os utilizadores que têm o trabalho do Azure Active Directory (Azure AD) contas escolares ou profissionais para aceder a aplicações com base na cloud que possuem permissões para. Os utilizadores que têm as edições do Azure AD também podem utilizar capacidades de gestão de aplicações através do painel de acesso e de grupos self-service.

Painel de acesso está separado do portal do Azure. Os usuários não precisam de uma subscrição do Azure para utilizar o painel de acesso.

Para utilizar com base em palavra-passe de início de sessão único (SSO) no painel de acesso, a extensão do painel de acesso tem de estar instalada no seu browser. A extensão transfere automaticamente ao selecionar uma aplicação que está configurada para SSO baseado em palavra-passe.

## <a name="browser-requirements-for-access-panel"></a>Requisitos de browser do painel de acesso

Painel de acesso requer um browser que suporte a JavaScript e CSS ativou o.

Os seguintes browsers suportam o SSO baseado em palavra-passe:

- Internet Explorer 8, 9, 10 e 11 no Windows 7 ou posterior

- No Windows 7 ou posterior ou no MacOS X ou posterior do Chrome

- Firefox 26.0 ou posterior no Windows XP SP2 ou posterior ou no Mac OS X 10.6 ou posterior

>[!NOTE]
>A extensão SSO baseado em palavra-passe se tornar disponível para o Microsoft Edge no Windows 10 quando o suporte para extensões do navegador foi adicionado ao Microsoft Edge.

## <a name="install-the-access-panel-browser-extension"></a>Instalar a extensão de Browser do painel de acesso

Siga estes passos.

1. Open [painel de acesso](https://myapps.microsoft.com) num browser suportado e inicie sessão como um utilizador no Azure AD.

2. Selecione uma aplicação com capacidade de SSO de palavra-passe no painel de acesso.

3. Quando lhe for pedido, selecione **instalar agora**.

4. Será redirecionado para uma ligação de transferência com base no seu browser. Selecione **adicionar** para instalar a extensão do browser.

5. Se lhe for pedido, selecione **habilitar** ou **permitir**.

6. Após a instalação, reinicie o navegador.

7.  Inicie sessão no painel de acesso e veja se pode iniciar as suas aplicações com capacidade de SSO de palavra-passe.

Pode transferir diretamente também as extensões para o Chrome e Firefox através destas ligações:

-   [Extensão do painel de acesso do Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extensão do painel de acesso do Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Configurar uma política de grupo para o Internet Explorer

Pode configurar uma política de grupo permite-lhe instalar remotamente a extensão do painel de acesso para o Internet Explorer nos computadores dos seus utilizadores.

Estes são os pré-requisitos:

-   [Serviços de domínio do Active Directory](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx) tem de ser definido, e os computadores dos seus utilizadores têm de ser associados ao seu domínio.

-   Tem "Editar definições de" permissão para editar o objeto de política de grupo (GPO). Por predefinição, os membros dos grupos de segurança seguintes têm esta permissão: Os administradores do domínio, administradores da empresa e proprietários de criador de políticas de grupo. [Saiba mais](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Para configurar a política de grupo e implementá-la aos utilizadores, veja [como implementar a extensão do painel de acesso para o Internet Explorer usando a diretiva de grupo](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy).

## <a name="troubleshoot-access-panel-in-internet-explorer"></a>Resolver problemas relacionados com o painel de acesso no Internet Explorer

Para aceder a uma ferramenta de diagnóstico e instruções para configurar a extensão, consulte [resolver problemas relacionados com a extensão do painel de acesso para o Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting).

## <a name="configure-password-sso-for-an-azure-ad-gallery-app"></a>Configurar a palavra-passe SSO para uma aplicação de galeria do Azure AD

Para configurar uma aplicação a partir da galeria do Azure AD, terá de fazer tudo isto:

-   Adicionar a aplicação a partir da galeria do Azure AD
-   [Configurar a aplicação para a palavra-passe início de sessão único](#configure-the-app-for-password-sso)
-   [Atribuir utilizadores à aplicação](#assign-users-to-the-app)

### <a name="add-the-app-from-the-azure-ad-gallery"></a>Adicionar a aplicação a partir da galeria do Azure AD

Siga estes passos.

1. Abra o [portal do Azure](https://portal.azure.com) e inicie sessão como um administrador global ou coadministrador.

2. Selecione **todos os serviços** na parte superior do painel de navegação no lado esquerdo para abrir a extensão do Azure AD.

3. Tipo **do Azure Active Directory** na caixa de pesquisa de filtro e, em seguida, selecione **Azure Active Directory**.

4. Selecione **aplicações empresariais** partir do painel de navegação do Azure AD.

5. Selecione **Add** no canto superior direito do **aplicações empresariais** painel.

6. Na **adicionar a partir da galeria** secção, escreva o nome da aplicação no **introduza um nome** caixa.

7. Selecione a aplicação que pretende configurar para SSO.

8. *Opcional:* Antes de adicionar a aplicação, pode alterar o seu nome na **nome** caixa.

9. Clique em **adicionar** para adicionar a aplicação.

   Após um breve atraso, poderá ver o painel de configuração da aplicação.

### <a name="configure-the-app-for-password-sso"></a>Configurar a aplicação SSO de palavra-passe

Siga estes passos.

1. Abra o [portal do Azure](https://portal.azure.com/) e inicie sessão como um administrador global ou coadministrador.

2. Selecione **todos os serviços** na parte superior do painel de navegação no lado esquerdo para abrir a extensão do Azure AD.

3. Tipo **do Azure Active Directory** na caixa de pesquisa de filtro e, em seguida, selecione **Azure Active Directory**.

4. Selecione **aplicações empresariais** no painel de navegação do Azure AD.

5. Selecione **todos os aplicativos** para ver uma lista das suas aplicações.

   > [!NOTE]
   > Se não vir a aplicação que pretende, utilize o **filtro** na parte superior do **todas as listas de aplicativos**. Definir o **mostrar** opção para "Todas as aplicações".

6. Selecione a aplicação que pretende configurar para SSO.

7. Depois da aplicação for carregada, selecione **início de sessão único** no painel do lado esquerdo da aplicação.

8. Selecione **baseado em palavra-passe de início de sessão** modo.

9. Atribua utilizadores à aplicação.

10. Também pode fornecer credenciais para os utilizadores. (Caso contrário, os utilizadores serão solicitados para introduzir as credenciais na inicialização do aplicativo.) Para tal, selecione as linhas dos utilizadores. Em seguida, selecione **as credenciais de atualização** e introduza os nomes de utilizador e palavras-passe.

### <a name="assign-users-to-the-app"></a>Atribuir utilizadores à aplicação

Para atribuir utilizadores a uma aplicação diretamente, siga estes passos:

1. Abra o [portal do Azure](https://portal.azure.com/) e inicie sessão como administrador global.

2. Selecione **todos os serviços** na dificuldade de navegação no lado esquerdo para abrir a extensão do Azure AD.

3. Tipo **do Azure Active Directory** na caixa de pesquisa de filtro e, em seguida, selecione **Azure Active Directory**.

4. Selecione **aplicações empresariais** no painel de navegação do Azure AD.

5. Selecione **todos os aplicativos** para ver uma lista das suas aplicações.

   > [!NOTE]
   > Se não vir a aplicação que pretende, utilize o **filtro** na parte superior do **todas as listas de aplicativos**. Definir o **mostrar** opção para "Todas as aplicações".

6. Na lista, selecione a aplicação que pretende atribuir um utilizador.

7. Depois da aplicação for carregada, selecione **utilizadores e grupos** da aplicação painel de navegação no lado esquerdo.

8. Selecione **Add** na parte superior a **utilizadores e grupos** lista para abrir o **adicionar atribuição** painel.

9. Selecione **utilizadores e grupos** no **adicionar atribuição** painel.

10. Na **pesquisar por nome ou endereço de e-mail** caixa, escreva o nome completo ou endereço de e-mail do utilizador que pretende atribuir.

11. Paire o rato sobre o utilizador na lista. Selecione a caixa de verificação junto a fotografia do perfil do usuário ou a logótipo para adicionar esse utilizador para o **selecionados** lista.

12. *Opcional:* Para adicionar outro utilizador, escreva outro nome ou endereço de e-mail no **pesquisar por nome ou endereço de e-mail** caixa e, em seguida, selecione a caixa de verificação para adicionar esse utilizador para o **selecionados** lista.

13. Quando tiver terminado de selecionar utilizadores, clique em **selecione** para adicioná-los à lista de utilizadores e grupos que são atribuídos à aplicação.

14. *Opcional:* Clique em **selecionar função** no **adicionar atribuição** painel para selecionar uma função para atribuir aos utilizadores que selecionou.

15. Selecione **atribuir** para atribuir a aplicação aos utilizadores selecionados.

    Após um breve atraso, os utilizadores serão capazes de aceder às aplicações do painel de acesso.

## <a name="request-support"></a>Pedido de suporte 
Se receber uma mensagem de erro ao configurar o SSO e atribuir utilizadores, abra um pedido de suporte. Inclua o máximo possível, as seguintes informações:

-   ID de correlação de erro
-   UPN (endereço de e-mail do utilizador)
-   TenantID
-   Tipo de navegador
-   Fuso horário e o período de tempo/tempo quando ocorreu o erro
-   Rastreios do fiddler

## <a name="next-steps"></a>Passos Seguintes
[Fornecer início de sessão único às suas aplicações com o Proxy de aplicações](application-proxy-configure-single-sign-on-with-kcd.md)
