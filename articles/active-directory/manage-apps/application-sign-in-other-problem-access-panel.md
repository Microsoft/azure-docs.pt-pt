---
title: Problemas ao entrar em um aplicativo no painel de acesso | Microsoft Docs
description: Como solucionar problemas de acesso a um aplicativo no painel de acesso Microsoft Azure AD em myapps.microsoft.com
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
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b93ee38666b93253c7cda6c756d4f58daaea236
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082148"
---
# <a name="problems-signing-in-to-an-application-from-the-access-panel"></a>Problemas ao entrar em um aplicativo no painel de acesso

O painel de acesso é um portal baseado na Web que permite a um usuário com uma conta corporativa ou de estudante no Azure Active Directory (Azure AD) exibir e iniciar aplicativos baseados em nuvem aos quais o administrador do Azure AD concedeu acesso. 

Esses aplicativos são configurados em nome do usuário no portal do AD do Azure. O aplicativo deve ser configurado corretamente e atribuído ao usuário ou a um grupo do qual o usuário é membro para ver o aplicativo no painel de acesso.

Os tipos de aplicativos que um usuário pode estar vendo estão nas seguintes categorias:

-   Aplicativos do Office 365

-   Aplicativos da Microsoft e de terceiros configurados com SSO baseado em Federação

-   Aplicativos de SSO baseados em senha

-   Aplicativos com soluções de SSO existentes

## <a name="general-issues-to-check-first"></a>Problemas gerais para verificar primeiro

-   Certifique-se de usar um **navegador** que atenda aos requisitos mínimos para o painel de acesso.

-   Verifique se o navegador do usuário adicionou a URL do aplicativo a seus **sites confiáveis**.

-   Certifique-se de verificar se o aplicativo está **configurado** corretamente.

-   Verifique se a conta do usuário está **habilitada** para entradas.

-   Verifique se a conta do usuário **não está bloqueada.**

-   Verifique se a senha do usuário **não expirou ou foi esquecida.**

-   Verifique se **a autenticação multifator** não está bloqueando o acesso do usuário.

-   Verifique se uma política de **acesso condicional** ou uma política de **proteção de identidade** não está bloqueando o acesso do usuário.

-   Verifique se as **informações de contato de autenticação** de um usuário estão atualizadas para permitir que as políticas de acesso condicional ou autenticação multifator sejam impostas.

-   Certifique-se também de tentar limpar os cookies do navegador e tentar entrar novamente.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Requisitos de navegador de reunião para o painel de acesso

O painel de acesso requer um navegador com suporte para JavaScript e com CSS habilitado. Para usar o SSO (logon único) baseado em senha no painel de acesso, a extensão do painel de acesso deve ser instalada no navegador do usuário. Essa extensão é baixada automaticamente quando um usuário seleciona um aplicativo configurado para SSO baseado em senha.

Para o SSO baseado em senha, os navegadores do usuário final podem ser:

-   Internet Explorer 8, 9, 10, 11--no Windows 7 ou posterior

-   Microsoft Edge no Windows 10 aniversário Edition ou posterior

-   Chrome – no Windows 7 ou posterior e no MacOS X ou posterior

-   Firefox 26,0 ou posterior – no Windows XP SP2 ou posterior e no Mac OS X 10,6 ou posterior

## <a name="how-to-install-the-access-panel-browser-extension"></a>Como instalar a extensão do navegador do painel de acesso

Para instalar a extensão do navegador do painel de acesso, siga as etapas abaixo:

1.  Abra o [painel de acesso](https://myapps.microsoft.com) em um dos navegadores com suporte e entre como um **usuário** no Azure AD.

2.  Clique em um **aplicativo de SSO de senha** no painel de acesso.

3.  No prompt solicitando a instalação do software, selecione **instalar agora**.

4.  Com base em seu navegador, você será direcionado para o link de download. **Adicione** a extensão ao seu navegador.

5.  Se seu navegador perguntar, selecione para **habilitar** ou **permitir** a extensão.

6.  Depois de instalado, **reinicie** a sessão do navegador.

7.  Entre no painel de acesso e veja se você pode **Iniciar** seus aplicativos de SSO de senha

Você também pode baixar a extensão para o Chrome e o Microsoft Edge a partir dos links diretos abaixo:

-   [Extensão do painel de acesso do Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extensão do painel de acesso do Microsoft Edge](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84)

## <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Como configurar o logon único federado para um aplicativo da galeria do Azure AD

Todos os aplicativos na galeria do Azure AD habilitados com o recurso de logon único corporativo têm um tutorial passo a passo disponível. Você pode acessar a [lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) para obter uma orientação passo a passo detalhada.

Para configurar um aplicativo da galeria do Azure AD, você precisa:

-   Adicionar um aplicativo da galeria do Azure AD

-   [Configurar os valores de metadados do aplicativo no Azure AD (URL de logon, identificador, URL de resposta)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Selecione o identificador de usuário e adicione atributos de usuário a serem enviados para o aplicativo](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Recuperar metadados e certificados do Azure AD](#download-the-azure-ad-metadata-or-certificate)

-   [Configurar os valores de metadados do Azure AD no aplicativo (URL de logon, emissor, URL de logout e certificado)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   Atribuir utilizadores à aplicação

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Adicionar um aplicativo da galeria do Azure AD

Para adicionar um aplicativo da galeria do Azure AD, siga as etapas abaixo:

1.  Abra o [portal do Azure](https://portal.azure.com) e entre como um **administrador global** ou **coadministrador**

2.  Abra a **extensão Azure Active Directory** clicando em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4.  clique em **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.

5.  Clique no botão **Adicionar** no canto superior direito do painel **aplicativos empresariais** .

6.  Na caixa de texto **Inserir um nome** na seção **Adicionar da Galeria** , digite o nome do aplicativo.

7.  Selecione o aplicativo que você deseja configurar para logon único.

8.  Antes de adicionar o aplicativo, você pode alterar seu nome na caixa de texto **nome** .

9.  Clique no botão **Adicionar** para adicionar o aplicativo.

Após um curto período, você poderá ver o painel de configuração do aplicativo.

### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Configurar o logon único para um aplicativo da galeria do Azure AD

Para configurar o logon único para um aplicativo, siga as etapas abaixo:

1. <span id="_Hlk477187909" class="anchor"><span id="_Hlk477001983" class="anchor"></span></span>Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global** ou **coadministrador.**

2. Abra a **extensão Azure Active Directory** clicando em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. clique em **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione o aplicativo para o qual você deseja configurar o logon único.

7. Depois que o aplicativo for carregado, clique no **logon único** no menu de navegação esquerdo do aplicativo.

8. Selecione **logon baseado em SAML** na lista suspensa **modo** .

9. Insira os valores necessários em **domínio e URLs.** Você deve obter esses valores do fornecedor do aplicativo.

   1. Para configurar o aplicativo como SSO iniciado por SP, a URL de logon é um valor necessário. Para alguns aplicativos, o identificador também é um valor necessário.

   2. Para configurar o aplicativo como SSO iniciado pelo IdP, a URL de resposta é um valor necessário. Para alguns aplicativos, o identificador também é um valor necessário.

10. **Opcional:** clique em **Mostrar configurações de URL avançadas** se desejar ver os valores não necessários.

11. Nos **atributos de usuário**, selecione o identificador exclusivo para seus usuários na lista suspensa **identificador de usuário** .

12. **Opcional:** clique em **Exibir e edite todos os outros atributos de usuário** para editar os atributos a serem enviados para o aplicativo no token SAML quando os usuários entrarem.

    Para adicionar um atributo:

    1. clique em **Adicionar atributo**. Insira o **nome** e selecione o **valor** na lista suspensa.

    2. Clique em **salvar.** O atributo novo é apresentado na tabela.

13. clique em **configurar &lt;nome do aplicativo&gt;** para acessar a documentação sobre como configurar o logon único no aplicativo. Além disso, você tem as URLs de metadados e o certificado necessários para configurar o SSO com o aplicativo.

14. Clique em **salvar** para salvar a configuração.

15. Atribua usuários ao aplicativo.

### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Selecione o identificador de usuário e adicione atributos de usuário a serem enviados para o aplicativo

Para selecionar o identificador de usuário ou adicionar atributos de usuário, siga as etapas abaixo:

1. Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global** ou **coadministrador.**

2. Abra a **extensão Azure Active Directory** clicando em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. clique em **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se você não vir o aplicativo que deseja exibir aqui, use o controle de **filtro** na parte superior da **lista todos os aplicativos** e defina a opção **Mostrar** como **todos os aplicativos.**

6. Selecione o aplicativo que você configurou o logon único.

7. Depois que o aplicativo for carregado, clique no **logon único** no menu de navegação esquerdo do aplicativo.

8. Na seção **atributos de usuário** , selecione o identificador exclusivo para seus usuários na lista suspensa **identificador de usuário** . A opção selecionada precisa corresponder ao valor esperado no aplicativo para autenticar o usuário.

   >[!NOTE]
   >Azure AD selecione o formato para o atributo NameID (identificador de usuário) com base no valor selecionado ou no formato solicitado pelo aplicativo no AuthRequest do SAML. Para obter mais informações, visite o artigo [protocolo SAML de logon único](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) na seção NameIDPolicy.
   >
   >

9. Para adicionar atributos de usuário, clique em **Exibir e editar todos os outros atributos de usuário** para editar os atributos a serem enviados ao aplicativo no token SAML quando os usuários entrarem.

   Para adicionar um atributo:

   1. clique em **Adicionar atributo**. Insira o **nome** e selecione o **valor** na lista suspensa.

   2. Clique em **salvar.** O atributo novo é apresentado na tabela.

### <a name="download-the-azure-ad-metadata-or-certificate"></a>Baixar o certificado ou metadados do Azure AD

Para baixar os metadados do aplicativo ou o certificado do Azure AD, siga as etapas abaixo:

1. Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global** ou **coadministrador.**

2. Abra a **extensão Azure Active Directory** clicando em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. clique em **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione o aplicativo que você configurou o logon único.

7. Depois que o aplicativo for carregado, clique no **logon único** no menu de navegação esquerdo do aplicativo.

8. Vá para a seção **certificado de autenticação SAML** e clique em **baixar** valor da coluna. Dependendo do que o aplicativo requer para configurar o logon único, você verá a opção para baixar o XML de metadados ou o certificado.

   O Azure AD não fornece uma URL para obter os metadados. Os metadados só podem ser recuperados como um arquivo XML.

## <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Como configurar o logon único federado para um aplicativo inexistente na Galeria

Para configurar um aplicativo inexistente na Galeria, você precisa ter o Azure AD Premium e o aplicativo dá suporte a SAML 2,0. Para obter mais informações sobre versões do Azure AD, visite [preços do Azure ad](https://azure.microsoft.com/pricing/details/active-directory/).

-   Configurar os valores de metadados do aplicativo no Azure AD (URL de logon, identificador, URL de resposta)

-   [Selecione o identificador de usuário e adicione atributos de usuário a serem enviados para o aplicativo](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Recuperar metadados e certificados do Azure AD](#download-the-azure-ad-metadata-or-certificate)

-   Configurar os valores de metadados do Azure AD no aplicativo (URL de logon, emissor, URL de logout e certificado)

### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>Configurar os valores de metadados do aplicativo no Azure AD (URL de logon, identificador, URL de resposta)

Para configurar o logon único para um aplicativo que não está na galeria do Azure AD, siga as etapas abaixo:

1. Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global** ou **coadministrador.**

2. Abra a **extensão Azure Active Directory** clicando em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. clique em **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.

5. Clique no botão **Adicionar** no canto superior direito do painel **aplicativos empresariais** .

6. clique em **aplicativo inexistente na Galeria** na seção **Adicionar seu próprio aplicativo** .

7. Insira o nome do aplicativo na caixa de texto **nome** .

8. Clique no botão **Adicionar** para adicionar o aplicativo.

9. Depois que o aplicativo for carregado, clique no **logon único** no menu de navegação esquerdo do aplicativo.

10. Selecione **logon baseado em SAML** na lista suspensa **modo**

11. Insira os valores necessários em **domínio e URLs.** Você deve obter esses valores do fornecedor do aplicativo.

    1. Para configurar o aplicativo como SSO iniciado por IdP, insira a URL de resposta e o identificador.

    2. **Opcional:** Para configurar o aplicativo como SSO iniciado por SP, a URL de logon é um valor necessário.

12. Nos **atributos de usuário**, selecione o identificador exclusivo para seus usuários na lista suspensa **identificador de usuário** .

13. **Opcional:** clique em **Exibir e edite todos os outros atributos de usuário** para editar os atributos a serem enviados para o aplicativo no token SAML quando os usuários entrarem.

    Para adicionar um atributo:

    1. clique em **Adicionar atributo**. Insira o **nome** e selecione o **valor** na lista suspensa.

    2. Clique em **salvar.** O atributo novo é apresentado na tabela.

14. clique em **configurar &lt;nome do aplicativo&gt;** para acessar a documentação sobre como configurar o logon único no aplicativo. Além disso, você tem URLs e certificados do Azure AD necessários para o aplicativo.

### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Selecione o identificador de usuário e adicione atributos de usuário a serem enviados para o aplicativo

Para selecionar o identificador de usuário ou adicionar atributos de usuário, siga as etapas abaixo:

1. Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global** ou **coadministrador.**

2. Abra a **extensão Azure Active Directory** clicando em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. clique em **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione o aplicativo que você configurou o logon único.

7. Depois que o aplicativo for carregado, clique no **logon único** no menu de navegação esquerdo do aplicativo.

8. Na seção **atributos de usuário** , selecione o identificador exclusivo para seus usuários na lista suspensa **identificador de usuário** . A opção selecionada precisa corresponder ao valor esperado no aplicativo para autenticar o usuário.

   >[!NOTE]
   >Azure AD selecione o formato para o atributo NameID (identificador de usuário) com base no valor selecionado ou no formato solicitado pelo aplicativo no AuthRequest do SAML. Para obter mais informações, visite o artigo [protocolo SAML de logon único](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) na seção NameIDPolicy.
   >
   >

9. Para adicionar atributos de usuário, clique em **Exibir e editar todos os outros atributos de usuário** para editar os atributos a serem enviados ao aplicativo no token SAML quando os usuários entrarem.

   Para adicionar um atributo:

   1. clique em **Adicionar atributo**. Insira o **nome** e selecione o **valor** na lista suspensa.

   2 clique em **salvar.** O atributo novo é apresentado na tabela.

### <a name="download-the-azure-ad-metadata-or-certificate"></a>Baixar o certificado ou metadados do Azure AD

Para baixar os metadados do aplicativo ou o certificado do Azure AD, siga as etapas abaixo:

1. Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global** ou **coadministrador.**

2. Abra a **extensão Azure Active Directory** clicando em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. clique em **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione o aplicativo que você configurou o logon único.

7. Depois que o aplicativo for carregado, clique no **logon único** no menu de navegação esquerdo do aplicativo.

8. Vá para a seção **certificado de autenticação SAML** e clique em **baixar** valor da coluna. Dependendo do que o aplicativo requer para configurar o logon único, você verá a opção para baixar o XML de metadados ou o certificado.

   O Azure AD não fornece uma URL para obter os metadados. Os metadados só podem ser recuperados como um arquivo XML.

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Como configurar o logon único com senha para um aplicativo da galeria do Azure AD

Para configurar um aplicativo da galeria do Azure AD, você precisa:

-   Adicionar um aplicativo da galeria do Azure AD

-   Configurar o aplicativo para logon único com senha

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Adicionar um aplicativo da galeria do Azure AD

Para adicionar um aplicativo da galeria do Azure AD, siga as etapas abaixo:

1.  Abra o [portal do Azure](https://portal.azure.com) e entre como um **administrador global** ou **coadministrador**

2.  Abra a **extensão Azure Active Directory** clicando em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4.  clique em **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.

5.  Clique no botão **Adicionar** no canto superior direito do painel **aplicativos empresariais** .

6.  Na caixa de texto **Inserir um nome** na seção **Adicionar da Galeria** , digite o nome do aplicativo

7.  Selecione a aplicação que quer configurar para início de sessão único

8.  Antes de adicionar o aplicativo, você pode alterar seu nome na caixa de texto **nome** .

9.  Clique no botão **Adicionar** para adicionar o aplicativo.

Após um curto período, você poderá ver o painel de configuração do aplicativo.

### <a name="configure-the-application-for-password-single-sign-on"></a>Configurar o aplicativo para logon único com senha

Para configurar o logon único para um aplicativo, siga as etapas abaixo:

1. Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global** ou **coadministrador.**

2. Abra a **extensão Azure Active Directory** clicando em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. clique em **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione o aplicativo para o qual você deseja configurar o logon único

7. Depois que o aplicativo for carregado, clique no **logon único** no menu de navegação esquerdo do aplicativo.

8. Selecione o modo de **logon baseado em senha.**

9. Atribua usuários ao aplicativo.

10. Além disso, você também pode fornecer credenciais em nome do usuário selecionando as linhas dos usuários e clicando em **Atualizar credenciais** e inserindo o nome de usuário e a senha em nome dos usuários. Caso contrário, os usuários serão solicitados a inserir as próprias credenciais na inicialização.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Como configurar o logon único com senha para um aplicativo inexistente na Galeria

Para configurar um aplicativo da galeria do Azure AD, você precisa:

-   [Adicionar um aplicativo inexistente na Galeria](#add-a-non-gallery-application)

-   [Configurar o aplicativo para logon único com senha](#configure-the-application-for-password-single-sign-on)

### <a name="add-a-non-gallery-application"></a>Adicionar um aplicativo inexistente na Galeria

Para adicionar um aplicativo da galeria do Azure AD, siga as etapas abaixo:

1.  Abra o [portal do Azure](https://portal.azure.com) e entre como um **administrador global** ou **coadministrador**

2.  Abra a **extensão Azure Active Directory** clicando em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4.  clique em **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.

5.  Clique no botão **Adicionar** no canto superior direito do painel **aplicativos empresariais** .

6.  clique em **aplicativo inexistente na galeria.**

7.  Insira o nome do seu aplicativo na caixa de texto **nome** . Selecione **Adicionar.**

Após um curto período, você poderá ver o painel de configuração do aplicativo.

### <a name="configure-the-application-for-password-single-sign-on"></a>Configurar o aplicativo para logon único com senha

Para configurar o logon único para um aplicativo, siga as etapas abaixo:

1. Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global** ou **coadministrador.**

2. Abra a **extensão Azure Active Directory** clicando em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. clique em **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione o aplicativo para o qual você deseja configurar o logon único.

7. Depois que o aplicativo for carregado, clique no **logon único** no menu de navegação esquerdo do aplicativo.

8. Selecione o modo de **logon baseado em senha.**

9. Insira a **URL de logon**. Essa é a URL em que os usuários inserem seu nome de usuário e senha para entrar. Verifique se os campos de entrada estão visíveis na URL.

10. Atribua usuários ao aplicativo.

11. Além disso, você também pode fornecer credenciais em nome do usuário selecionando as linhas dos usuários e clicando em **Atualizar credenciais** e inserindo o nome de usuário e a senha em nome dos usuários. Caso contrário, os usuários serão solicitados a inserir as próprias credenciais na inicialização.

## <a name="how-to-assign-a-user-to-an-application-directly"></a>Como atribuir um usuário diretamente a um aplicativo

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

12. **Opcional:** se quiser **adicionar mais do que um utilizador**, tipo em outro **nome completo** ou **endereço de e-mail** para o **procurar por nome ou endereço de e-mail** caixa de pesquisa e clique na caixa de verificação para adicionar este utilizador para o **selecionados** lista.

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

## <a name="next-steps"></a>Passos seguintes
[Fornecer início de sessão único às suas aplicações com o Proxy de aplicações](application-proxy-configure-single-sign-on-with-kcd.md)

