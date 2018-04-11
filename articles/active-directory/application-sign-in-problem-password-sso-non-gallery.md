---
title: Problemas em iniciar sessão a uma aplicação de galeria do Azure AD configurada para a palavra-passe de sessão único-| Microsoft Docs
description: Aborda áreas problemáticas que fornecem orientações para resolver problemas relacionados com o início de sessão aplicações do Azure AD Galeria configuradas para a palavra-passe-início de sessão único
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: c01bf353c9617811e14a3d60f91584b57ac0b5e7
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2018
---
# <a name="problems-signing-in-to-an-azure-ad-gallery-application-configured-for-password-single-sign-on"></a>Problemas em iniciar sessão a uma aplicação de galeria do Azure AD configurada para a palavra-passe-início de sessão único

O painel de acesso é um portal baseado na web que permite que um utilizador que tenha uma conta escolar ou profissional no Azure Active Directory (Azure AD) para ver e iniciar baseado na nuvem as aplicações que o administrador do Azure AD concedeu-lhes acesso. Um utilizador que possua as edições do Azure AD também pode utilizar grupos self-service e capacidades de gestão de aplicações através do painel de acesso. O painel de acesso separado do portal do Azure e não exige que os utilizadores tenham uma subscrição do Azure.

Para utilizar com base em palavra-passe-início de sessão único (SSO) no painel de acesso, a extensão do painel de acesso tem de estar instalada no browser do utilizador. Esta extensão é transferida automaticamente quando um utilizador seleciona uma aplicação que está configurada para SSO baseada em palavra-passe.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Que cumprem os requisitos de browser para o painel de acesso

O painel de acesso necessita de um browser que suporte JavaScript e ativou CSS. Para utilizar com base em palavra-passe-início de sessão único (SSO) no painel de acesso, a extensão do painel de acesso tem de estar instalada no browser do utilizador. Esta extensão é transferida automaticamente quando um utilizador seleciona uma aplicação que está configurada para SSO baseada em palavra-passe.

Para SSO baseada em palavra-passe, os browsers do utilizador final podem ser:

-   Internet Explorer 8, 9, 10, 11 - no Windows 7 ou posterior

-   Chrome – No Windows 7 ou posterior e no MacOS X ou posterior

-   Firefox 26.0 ou posterior – no Windows XP SP2 ou posterior e no Mac OS X 10.6 ou posterior

>[!NOTE]
>A extensão SSO baseada em palavra-passe fiquem disponíveis para Microsoft Edge no Windows 10 quando as extensões de browser ficarem suportadas para o limite.
>
>

## <a name="how-to-install-the-access-panel-browser-extension"></a>Como instalar a extensão de Browser do painel de acesso

Para instalar a extensão de Browser do painel de acesso, siga os passos abaixo:

1.  Abra o [painel de acesso](https://myapps.microsoft.com) dos browsers suportados e início de sessão como um **utilizador** no seu Azure AD.

2.  Clique num **aplicação de palavra-passe SSO** no painel de acesso.

3.  Na linha de pedir instalar o software, selecione **instalar agora**.

4.  Com base no seu browser, direcionado para a hiperligação de transferência. **Adicionar** a extensão para o seu browser.

5.  Se o browser pede-lhe, selecione como **ativar** ou **permitir** a extensão.

6.  Uma vez instalado, **reiniciar** a sessão do browser.

7.  Inicie sessão no painel de acesso e veja se pode **iniciar** as aplicações de SSO de palavra-passe

Também pode transferir a extensão para Chrome e Firefox a partir de hiperligações diretas abaixo:

-   [Extensão de painel de acesso do Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extensão de painel de acesso de Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="setting-up-a-group-policy-for-internet-explorer"></a>Configurar uma política de grupo para o Internet Explorer

Pode configurar uma política de grupo que lhe permitem instalar remotamente a extensão do painel de acesso para o Internet Explorer em máquinas dos seus utilizadores.

Os pré-requisitos incluem:

-   Configurou [serviços de domínio do Active Directory](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx), e associar máquinas dos utilizadores ao seu domínio.

-   Tem de ter a permissão "Definições de editar" para editar o objeto de política de grupo (GPO). Por predefinição, os membros dos grupos de segurança seguinte têm esta permissão: os administradores do domínio, administradores da empresa e proprietários de criador de política de grupo. [Saiba mais](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Siga o tutorial [como implementar a extensão do painel de acesso para o Internet Explorer utilizando a política de grupo](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy) para instruções passo a passo sobre como configurar a política de grupo e implementá-la aos utilizadores.

## <a name="troubleshoot-the-access-panel-in-internet-explorer"></a>Resolver problemas relacionados com o painel de acesso no Internet Explorer

Siga o [resolver problemas relacionados com a extensão do painel de acesso para o Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-Troubleshoot) guia para acesso de uma ferramenta de diagnóstico e instruções passo a passo sobre como configurar a extensão de i/e.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Como configurar a palavra-passe-início de sessão único para uma aplicação não Galeria

Para configurar uma aplicação na galeria do Azure AD que tem de:

-   [Adicionar uma aplicação não Galeria](#add-a-non-gallery-application)

-   [Configurar a aplicação para a palavra-passe-início de sessão único](#configure-the-application-for-password-single-sign-on)

-   [Atribuir utilizadores para a aplicação](#assign-users-to-the-application)

### <a name="add-a-non-gallery-application"></a>Adicionar uma aplicação não Galeria

Para adicionar uma aplicação na galeria do Azure AD, siga os passos abaixo:

1.  Abra o [portal do Azure](https://portal.azure.com) e inicie sessão como um **Administrador Global** ou **coadministrador**

2.  Abra o **extensão do Active Directory do Azure** clicando **todos os serviços** no topo do menu de navegação esquerdo principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** no menu de navegação esquerdo do Azure Active Directory.

5.  Clique em de **adicionar** botão no canto superior direito no **aplicações empresariais** painel.

6.  Clique em **aplicação não galeria.**

7.  Introduza o nome da sua aplicação no **nome** caixa de texto. Selecione **adicionar.**

Após um curto período de tempo, poderá ver o painel de configuração da aplicação.

### <a name="configure-the-application-for-password-single-sign-on"></a>Configurar a aplicação para a palavra-passe-início de sessão único

Para configurar o início de sessão para uma aplicação, siga os passos abaixo:

1.  Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Co-administrador.**

2.  Abra o **extensão do Active Directory do Azure** clicando **todos os serviços** no topo do menu de navegação esquerdo principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** no menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **todas as aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que pretende mostrar aqui, utilize o **filtro** controlo na parte superior a **lista de todas as aplicações** e defina o **mostrar** opção para **todos os Aplicações.**

6.  Selecione a aplicação que pretende configurar o início de sessão único

7.  Quando carrega a aplicação, clique em de **de sessão único-** a partir do menu de navegação esquerdo da aplicação.

8.  Selecione o modo **baseada em palavra-passe de início de sessão.**

9.  Introduza o **URL de início de sessão**. Este é o URL onde os utilizadores introduzem o respetivo nome de utilizador e palavra-passe para iniciar sessão no. Certifique-se de que os campos de início de sessão está visível no URL.

10. Atribua utilizadores para a aplicação.

11. Além disso, também pode fornecer as credenciais em nome do utilizador ao selecionar as linhas dos utilizadores e clicar no **credenciais de atualização** e introduzindo o nome de utilizador e palavra-passe em nome de utilizadores. Caso contrário, os utilizadores ser pedido que introduza as credenciais próprios após iniciar.

### <a name="assign-users-to-the-application"></a>Atribuir utilizadores para a aplicação

Para atribuir diretamente um ou mais utilizadores a uma aplicação, siga os passos abaixo:

1.  Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** clicando **todos os serviços** no topo do menu de navegação esquerdo principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** no menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **todas as aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que pretende mostrar aqui, utilize o **filtro** controlo na parte superior a **lista de todas as aplicações** e defina o **mostrar** opção para **todos os Aplicações.**

6.  Selecione a aplicação que pretende atribuir um utilizador a partir da lista.

7.  Quando carrega a aplicação, clique em **utilizadores e grupos** a partir do menu de navegação esquerdo da aplicação.

8.  Clique em de **adicionar** botão do **utilizadores e grupos** lista para abrir o **adicionar atribuição** painel.

9.  Clique em de **utilizadores e grupos** Seletor do **adicionar atribuição** painel.

10. Escreva o **nome completo** ou **endereço de correio eletrónico** do utilizador que está interessado atribuir para o **pesquisa por nome ou endereço de e-mail** caixa de pesquisa.

11. Coloque o cursor sobre o **utilizador** na lista de revela um **caixa de verificação**. Clique na caixa de verificação junto a fotografia do perfil do utilizador ou logótipo para adicionar o utilizador para o **selecionados** lista.

12. **Opcional:** se gostaria de **adicionar mais do que um utilizador**, tipo noutra **nome completo** ou **endereço de correio eletrónico** para o **pesquisar por nome ou endereço de correio eletrónico** caixa de pesquisa e clique na caixa de verificação para adicionar este utilizador para o **selecionados** lista.

13. Quando tiver terminado de selecionar utilizadores, clique em de **selecione** botão para os adicionar à lista de utilizadores e grupos atribuídos à aplicação.

14. **Opcional:** clique o **selecionar função** Seletor no **adicionar atribuição** painel para selecionar uma função para atribuir aos utilizadores que selecionou.

15. Clique em de **atribuir** botão para atribuir a aplicação para os utilizadores selecionados.

Após um curto período de tempo, os utilizadores que selecionou conseguir iniciar estas aplicações no painel de acesso.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Se efetue estes passos de resolução de problemas não a resolva o problema

Abra um pedido de suporte com as seguintes informações se disponíveis:

-   ID de correlação de erro

-   UPN (endereço de e-mail do utilizador)

-   TenantID

-   Tipo de browser

-   Fuso horário e tempo/período de tempo durante o erro ocorre

-   Rastreios de fiddler

## <a name="next-steps"></a>Passos Seguintes
[Fornecer início de sessão único às suas aplicações com o Proxy da aplicação](active-directory-application-proxy-sso-using-kcd.md)

