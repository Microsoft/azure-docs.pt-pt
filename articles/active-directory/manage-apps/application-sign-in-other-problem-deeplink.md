---
title: Problemas ao iniciar sessão para uma aplicação com um deeplink | Documentos da Microsoft
description: Como resolver problemas de acesso à aplicação a partir de um deeplink URL com o Azure AD
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
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3525436c61038d9cdda3a1b1da8e226ab153134f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58098258"
---
# <a name="problems-signing-in-to-an-application-using-a-deeplink"></a>Problemas ao iniciar sessão para uma aplicação com um deeplink

O painel de acesso é um portal baseado na web que permite que um utilizador com uma conta profissional ou escolar no Azure Active Directory (Azure AD) para ver e iniciar os aplicativos baseados na nuvem que o administrador do Azure AD lhe concedeu-lhes acesso. 

Esses aplicativos são configurados em nome do utilizador no portal do Azure AD. A aplicação tem de ser configurada corretamente e atribuída ao usuário ou um grupo que o utilizador é membro de para ver a aplicação no painel de acesso.

Acesso de utilizador ou de ligações avançadas URLs são ligações, que os utilizadores podem utilizar para aceder às suas aplicações de SSO de palavra-passe diretamente a partir de suas barras de URL de navegadores. Ao navegar para esta ligação, os utilizadores ser automaticamente iniciada no aplicativo sem ter de ir para o painel de acesso pela primeira vez. Esta é a mesma ligação que os utilizadores utilizam para aceder a estas aplicações a partir do iniciador de aplicações do Office 365.

## <a name="general-issues-to-check-first"></a>Problemas gerais para verificar primeiro

-   Certifique-se de que sua com um **browser** que cumpra os requisitos mínimos para o painel de acesso.

-   Certifique-se de que o navegador do usuário adicionou o URL da aplicação para o seu **sites fidedignos**.

-   Certifique-se verificar a aplicação está **configurado** corretamente.

-   Certifique-se a conta de utilizador está **ativada** para inícios de sessão.

-   Certifique-se a conta de utilizador é **não bloqueadas.**

-   Certificar-se de que o usuário **palavra-passe não está expirado ou esquecido.**

-   Certifique-se **multi-factor Authentication** não está a bloquear o acesso dos utilizadores.

-   Certifique-se de um **política de acesso condicional** ou **Identity Protection** política não está a bloquear o acesso de utilizador.

-   Certifique-se de que um usuário **informações de contacto de autenticação** está atualizada para permitir que as políticas de acesso condicional ou de multi-factor Authentication sejam impostas.

-   Certifique-se para também tente limpar os cookies de seu navegador e tentar iniciar sessão novamente.

## <a name="checking-the-deeplink"></a>A verificação do deeplink

Para verificar se tem o deeplink correto, siga estes passos:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Coadministrador.**

2. Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Coadministrador.**

7. Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

8. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

9. Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

10. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

    * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

11. Selecione a aplicação que pretende que a verificação do deeplink para.

12. Localize a etiqueta **URL de acesso de utilizador**. Sua deeplink deve corresponder a este URL.

## <a name="how-to-install-the-access-panel-browser-extension"></a>Como instalar a extensão de Browser do painel de acesso

Para instalar a extensão de Browser do painel de acesso, siga estes passos:

1.  Abra o [painel de acesso](https://myapps.microsoft.com) em um dos browsers suportados e inicie sessão como um **utilizador** no seu Azure AD.

2.  Clique num **aplicação SSO de palavra-passe** no painel de acesso.

3.  Na linha de comandos pedindo para instalar o software, selecione **instalar agora**.

4.  Com base no seu navegador, que será direcionado para a ligação de transferência. **Adicionar** a extensão ao seu navegador.

5.  Se o browser pede-lhe, selecione a qualquer um **habilitar** ou **permitir** a extensão.

6.  Depois de instalada **reiniciar** a sessão do browser.

7.  Iniciar sessão no painel de acesso e veja se pode **inicie** seus aplicativos de SSO de palavra-passe

Também pode baixar a extensão para o Chrome e Firefox destes links diretos:

-   [Extensão de painel de acesso do Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extensão do painel de acesso do Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Como configurar a palavra-passe início de sessão único para uma aplicação da galeria do Azure AD

Para configurar uma aplicação da galeria do Azure AD, faça o seguinte:

-   [Adicionar uma aplicação a partir da galeria do Azure AD](#add-an-application-from-the-Azure-AD-gallery)

-   [Configurar a aplicação para a palavra-passe início de sessão único](#configure-the-application-for-password-single-sign-on)

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Adicionar uma aplicação a partir da galeria do Azure AD

Para adicionar uma aplicação da galeria do Azure AD, siga estes passos:

1.  Abra o [portal do Azure](https://portal.azure.com) e inicie sessão como um **Administrador Global** ou **coadministrador**.

2.  Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4.  Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5.  Clique nas **Add** botão no canto superior direito a **aplicações empresariais** painel.

6.  Na **introduza um nome** caixa de texto da **adicionar a partir da galeria** secção, escreva o nome da aplicação.

7.  Selecione a aplicação que pretende configurar para início de sessão único.

8.  Antes de adicionar a aplicação, pode alterar o nome a partir da **nome** caixa de texto.

9.  Para adicionar a aplicação, clique em **adicionar**.

Após um curto período, são capazes de ver o painel de configuração do aplicativo.

### <a name="configure-the-application-for-password-single-sign-on"></a>Configurar a aplicação para a palavra-passe início de sessão único

Para configurar o início de sessão único para uma aplicação, siga estes passos:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Coadministrador.**

2. Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione a aplicação que pretende configurar o início de sessão único.

7. Assim que o aplicativo é carregado, clique nas **início de sessão único** no menu de navegação do lado esquerdo da aplicação.

8. Selecione o modo **baseado em palavra-passe de início de sessão.**

9. [Atribuir utilizadores à aplicação](#how-to-assign-a-user-to-an-application-directly).

10. Além disso, também pode fornecer as credenciais em nome do utilizador ao selecionar as linhas dos utilizadores e clicar em **as credenciais de atualização** e introduzindo o nome de utilizador e palavra-passe em nome dos utilizadores. Caso contrário, ser pedido aos utilizadores para introduzir as credenciais durante a inicialização propriamente ditas.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Como configurar a palavra-passe início de sessão único para uma aplicação de externas à Galeria

Para configurar uma aplicação da galeria do Azure AD, faça o seguinte:

-   [Adicionar uma aplicação de externas à Galeria](#add-a-non-gallery-application)

-   [Configurar a aplicação para a palavra-passe início de sessão único](#configure-the-application-for-password-single-sign-on)

### <a name="add-a-non-gallery-application"></a>Adicionar uma aplicação de externas à Galeria

Para adicionar uma aplicação da galeria do Azure AD, siga estes passos:

1.  Abra o [portal do Azure](https://portal.azure.com) e inicie sessão como um **Administrador Global** ou **coadministrador**.

2.  Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4.  Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5.  Clique nas **Add** botão no canto superior direito a **aplicações empresariais** painel.

6.  Clique em **aplicação da Galeria não.**

7.  Introduza o nome da sua aplicação no **nome** caixa de texto. Selecione **adicionar.**

Após um curto período, são capazes de ver o painel de configuração do aplicativo.

### <a name="configure-the-application-for-password-single-sign-on"></a>Configurar a aplicação para a palavra-passe início de sessão único

Para configurar o início de sessão único para uma aplicação, siga estes passos:

1.  Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Coadministrador.**

2.  Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4.  Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5.  Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

    1.  Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6.  Selecione a aplicação que pretende configurar o início de sessão único.

7.  Assim que o aplicativo é carregado, clique nas **início de sessão único** no menu de navegação do lado esquerdo da aplicação.

8.  Selecione o modo **baseado em palavra-passe de início de sessão.**

9.  Introduza o **URL de início de sessão**, o URL em que os utilizadores introduzirem o respetivo nome de utilizador e palavra-passe para iniciar sessão. Certifique-se de que os campos de início de sessão são visíveis no URL.

10. Atribua utilizadores à aplicação.

11. Além disso, também pode fornecer as credenciais em nome do utilizador ao selecionar as linhas dos utilizadores e clicar em **as credenciais de atualização** e introduzindo o nome de utilizador e palavra-passe em nome dos utilizadores. Caso contrário, ser pedido aos utilizadores para introduzir as credenciais durante a inicialização propriamente ditas.

## <a name="how-to-assign-a-user-to-an-application-directly"></a>Como atribuir um utilizador a uma aplicação diretamente

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

11. Paire o rato sobre o **usuário** na lista para revelar uma **caixa de verificação**. Para adicionar o utilizador para o **selecionados** lista, clique na caixa de verificação junto a fotografia do perfil ou à logótipo do utilizador.

12. **Opcional:** Se quiser **adicionar mais de um usuário**, tipo em outro **nome completo** ou **endereço de e-mail** para o **procurar por nome ou endereço de e-mail** caixa de pesquisa e clique na caixa de verificação para adicionar este utilizador para o **selecionados** lista.

13. Quando tiver terminado de selecionar utilizadores, clique nas **selecione** botão para adicioná-los à lista de utilizadores e grupos que devem ser atribuídos à aplicação.

14. **Opcional:** clique a **selecionar função** Seletor no **adicionar atribuição** painel para selecionar uma função para atribuir aos utilizadores que selecionou.

15. Clique nas **atribuir** botão para atribuir a aplicação aos utilizadores selecionados.

Após um curto período, os utilizadores que selecionou ser capaz de iniciar estas aplicações no painel de acesso.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Se estes passos de resolução de problemas não o resolve o problema. 

Abra um pedido de suporte com as seguintes informações se estiver disponível:

-   ID de correlação de erro

-   UPN (endereço de e-mail do utilizador)

-   TenantID

-   Tipo de navegador

-   Fuso horário e tempo/período de tempo durante o erro ocorre

-   Rastreios do fiddler

## <a name="next-steps"></a>Passos Seguintes
[Fornecer início de sessão único às suas aplicações com o Proxy de aplicações](application-proxy-configure-single-sign-on-with-kcd.md)
