---
title: Problemas ao iniciar sessão a uma aplicação a partir do painel de acesso | Documentos da Microsoft
description: Como resolver problemas de acesso à aplicação a partir do painel de acesso do Microsoft Azure AD em myapps.microsoft.com
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
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 691022762e63687c298090c02f486993b4fd77a5
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58076875"
---
# <a name="problems-signing-in-to-an-application-from-the-access-panel"></a>Problemas ao iniciar sessão a uma aplicação a partir do painel de acesso

O painel de acesso é um portal baseado na web que permite que um utilizador com uma conta profissional ou escolar no Azure Active Directory (Azure AD) para ver e iniciar os aplicativos baseados na nuvem que o administrador do Azure AD lhe concedeu-lhes acesso. 

Esses aplicativos são configurados em nome do utilizador no portal do Azure AD. A aplicação tem de ser configurada corretamente e atribuída ao usuário ou um grupo que o utilizador é membro de para ver a aplicação no painel de acesso.

O tipo de aplicações pode estar a ver um utilizador se enquadram nas seguintes categorias:

-   Aplicativos do Office 365

-   Aplicativos Microsoft e de terceiros configurados com o SSO baseado em Federação

-   Aplicativos de SSO de palavra-passe

-   Aplicações com soluções existentes de SSO

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

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Requisitos de browser de reunião para o painel de acesso

O painel de acesso requer um browser que suporte a JavaScript e CSS ativou o. Para utilizar com base em palavra-passe de início de sessão único (SSO) no painel de acesso, a extensão do painel de acesso tem de estar instalada no navegador do usuário. Esta extensão é transferida automaticamente quando um usuário seleciona uma aplicação que está configurada para SSO baseado em palavra-passe.

Para SSO de palavra-passe, os navegadores do usuário final podem ser:

-   Internet Explorer 8, 9, 10, 11 – no Windows 7 ou posterior

-   Microsoft Edge no Windows 10 Anniversary Edition ou posterior

-   Cromado, No Windows 7 ou posterior e no MacOS X ou posterior

-   Firefox 26.0 ou mais tarde, no Windows XP SP2 ou posterior e no Mac OS X 10.6 ou posterior

## <a name="how-to-install-the-access-panel-browser-extension"></a>Como instalar a extensão de Browser do painel de acesso

Para instalar a extensão de Browser do painel de acesso, siga os passos abaixo:

1.  Abra o [painel de acesso](https://myapps.microsoft.com) em um dos browsers suportados e inicie sessão como um **utilizador** no seu Azure AD.

2.  Clique num **aplicação SSO de palavra-passe** no painel de acesso.

3.  Na linha de comandos pedindo para instalar o software, selecione **instalar agora**.

4.  Com base no seu navegador, que será direcionado para a ligação de transferência. **Adicionar** a extensão ao seu navegador.

5.  Se o browser pede-lhe, selecione a qualquer um **habilitar** ou **permitir** a extensão.

6.  Depois de instalada **reiniciar** a sessão do browser.

7.  Iniciar sessão no painel de acesso e veja se pode **inicie** seus aplicativos de SSO de palavra-passe

Também pode baixar a extensão para o Chrome e o Microsoft Edge a partir das ligações diretas abaixo:

-   [Extensão de painel de acesso do Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extensão de painel de acesso do Microsoft Edge](https://www.microsoft.com/store/apps/9pc9sckkzk84)

## <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Como configurar o início de sessão único federado para uma aplicação da galeria do Azure AD

Todas as aplicações na galeria do Azure AD, ativada com capacidade de Enterprise Single Sign-On tem um tutorial passo a passo disponível. Pode aceder a [lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) para uma orientação passo a passo de detalhe.

Para configurar uma aplicação da galeria do Azure AD, que tem de:

-   Adicionar uma aplicação a partir da galeria do Azure AD

-   [Configurar valores de metadados da aplicação no Azure AD (URL de resposta de URL, identificador, início de sessão)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Selecione o identificador de utilizador e adicionar atributos de utilizador sejam enviados para a aplicação](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Obter metadados do Azure AD e o certificado](#download-the-azure-ad-metadata-or-certificate)

-   [Configurar valores de metadados do Azure AD no aplicativo (início de sessão no URL, o emissor, o URL de fim de sessão e o certificado)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   Atribuir utilizadores à aplicação

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Adicionar uma aplicação a partir da galeria do Azure AD

Para adicionar uma aplicação da galeria do Azure AD, siga os passos abaixo:

1.  Abra o [portal do Azure](https://portal.azure.com) e inicie sessão como um **Administrador Global** ou **coadministrador**

2.  Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4.  Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5.  Clique nas **Add** botão no canto superior direito a **aplicações empresariais** painel.

6.  Na **introduza um nome** caixa de texto da **adicionar a partir da galeria** secção, escreva o nome da aplicação.

7.  Selecione a aplicação que pretende configurar para início de sessão único.

8.  Antes de adicionar a aplicação, pode alterar o nome a partir da **nome** caixa de texto.

9.  Clique em **adicionar** botão, para adicionar a aplicação.

Após um curto período, pode ver o painel de configuração do aplicativo.

### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Configurar o início de sessão único para uma aplicação da galeria do Azure AD

Para configurar o início de sessão único para uma aplicação, siga os passos abaixo:

1. <span id="_Hlk477187909" class="anchor"><span id="_Hlk477001983" class="anchor"></span></span>Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Coadministrador.**

2. Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione a aplicação que pretende configurar o início de sessão único.

7. Assim que o aplicativo é carregado, clique nas **início de sessão único** no menu de navegação do lado esquerdo da aplicação.

8. Selecione **baseado em SAML logon** da **modo** lista pendente.

9. Introduza os valores necessários no **domínio e URLs.** Deve obter estes valores do fabricante da aplicação.

   1. Para configurar a aplicação como SSO iniciado por SP, o URL de início de sessão é um valor obrigatório. Para alguns aplicativos, o identificador é também um valor obrigatório.

   2. Para configurar a aplicação como SSO iniciado por IdP, o URL de resposta é um valor obrigatório. Para alguns aplicativos, o identificador é também um valor obrigatório.

10. **Opcional:** clique em **Mostrar definições de URL avançadas** se quiser ver os valores não é necessário.

11. Na **atributos de utilizador**, selecione o identificador exclusivo para os seus utilizadores no **identificador de utilizador** lista pendente.

12. **Opcional:** clique em **ver e editar todos os outros atributos de utilizador** para editar os atributos a serem enviados para a aplicação no SAML token, quando os utilizadores iniciam sessão.

    Para adicionar um atributo:

    1. Clique em **adicionar atributo**. Introduza o **Name** e selecione o **valor** na lista pendente.

    2. Clique em **guardar.** O atributo novo é apresentado na tabela.

13. Clique em **configurar &lt;nome da aplicação&gt;**  para aceda à documentação sobre como configurar o início de sessão único no aplicativo. Além disso, tem os URLs de metadados e o certificado necessário para configurar o SSO com a aplicação.

14. Clique em **guardar** para guardar a configuração.

15. Atribua utilizadores à aplicação.

### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Selecione o identificador de utilizador e adicionar atributos de utilizador sejam enviados para a aplicação

Para selecionar o identificador de utilizador ou adicionar atributos de utilizador, siga os passos abaixo:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Coadministrador.**

2. Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que pretende apresentadas aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de  **Todas as aplicações.**

6. Selecione a aplicação tiver configurado o início de sessão único.

7. Assim que o aplicativo é carregado, clique nas **início de sessão único** no menu de navegação do lado esquerdo da aplicação.

8. Sob o **atributos de utilizador** secção, selecione o identificador exclusivo para os seus utilizadores no **identificador de utilizador** lista pendente. A opção selecionada tem de corresponder ao valor esperado na aplicação para autenticar o utilizador.

   >[!NOTE]
   >O Azure AD selecionar o formato para o atributo NameID (identificador de utilizador) com base no valor selecionado ou o formato de pedido com o aplicativo em AuthRequest o SAML. Para obter mais informações, visite o artigo [protocolo SAML de início de sessão único](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) na secção NameIDPolicy.
   >
   >

9. Para adicionar os atributos de utilizador, clique em **ver e editar todos os outros atributos de utilizador** para editar os atributos a serem enviados para a aplicação no SAML token, quando os utilizadores iniciam sessão.

   Para adicionar um atributo:

   1. Clique em **adicionar atributo**. Introduza o **Name** e selecione o **valor** na lista pendente.

   2. Clique em **guardar.** O atributo novo é apresentado na tabela.

### <a name="download-the-azure-ad-metadata-or-certificate"></a>Transferir os metadados do Azure AD ou o certificado

Para transferir os metadados da aplicação ou o certificado do Azure AD, siga os passos abaixo:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Coadministrador.**

2. Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione a aplicação tiver configurado o início de sessão único.

7. Assim que o aplicativo é carregado, clique nas **início de sessão único** no menu de navegação do lado esquerdo da aplicação.

8. Aceda a **certificado de assinatura SAML** secção, em seguida, clique em **transferir** valor da coluna. Consoante o que o aplicativo requer configurar início de sessão único, verá a opção para transferir o XML de metadados ou o certificado.

   O Azure AD não fornece um URL para obter os metadados. Os metadados só podem ser obtidos como um arquivo XML.

## <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Como configurar o início de sessão único federado para uma aplicação de externas à Galeria

Para configurar uma aplicação de externas à galeria, tem de ter o Azure AD premium e a aplicação suporta SAML 2.0. Para obter mais informações sobre as versões do AD do Azure, visite [preços do Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

-   Configurar valores de metadados da aplicação no Azure AD (URL de resposta de URL, identificador, início de sessão)

-   [Selecione o identificador de utilizador e adicionar atributos de utilizador sejam enviados para a aplicação](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Obter metadados do Azure AD e o certificado](#download-the-azure-ad-metadata-or-certificate)

-   Configurar valores de metadados do Azure AD no aplicativo (início de sessão no URL, o emissor, o URL de fim de sessão e o certificado)

### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>Configurar valores de metadados da aplicação no Azure AD (URL de resposta de URL, identificador, início de sessão)

Para configurar o início de sessão único para uma aplicação que não esteja na galeria do Azure AD, siga os passos abaixo:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Coadministrador.**

2. Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5. Clique nas **Add** botão no canto superior direito a **aplicações empresariais** painel.

6. Clique em **aplicação da Galeria não** no **adicionar sua própria aplicação** secção.

7. Introduza o nome do aplicativo na **nome** caixa de texto.

8. Clique em **adicionar** botão, para adicionar a aplicação.

9. Assim que o aplicativo é carregado, clique nas **início de sessão único** no menu de navegação do lado esquerdo da aplicação.

10. Selecione **baseado em SAML logon** no **modo** pendente

11. Introduza os valores necessários no **domínio e URLs.** Deve obter estes valores do fabricante da aplicação.

    1. Para configurar a aplicação como SSO iniciado por IdP, introduza o URL de resposta e o identificador.

    2. **Opcional:** Para configurar a aplicação como SSO iniciado por SP, o URL de início de sessão é um valor obrigatório.

12. Na **atributos de utilizador**, selecione o identificador exclusivo para os seus utilizadores no **identificador de utilizador** lista pendente.

13. **Opcional:** clique em **ver e editar todos os outros atributos de utilizador** para editar os atributos a serem enviados para a aplicação no SAML token, quando os utilizadores iniciam sessão.

    Para adicionar um atributo:

    1. Clique em **adicionar atributo**. Introduza o **Name** e selecione o **valor** na lista pendente.

    2. Clique em **guardar.** O atributo novo é apresentado na tabela.

14. Clique em **configurar &lt;nome da aplicação&gt;**  para aceda à documentação sobre como configurar o início de sessão único no aplicativo. Além disso, terá de URLs do Azure AD e o certificado necessário para a aplicação.

### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Selecione o identificador de utilizador e adicionar atributos de utilizador sejam enviados para a aplicação

Para selecionar o identificador de utilizador ou adicionar atributos de utilizador, siga os passos abaixo:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Coadministrador.**

2. Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione a aplicação tiver configurado o início de sessão único.

7. Assim que o aplicativo é carregado, clique nas **início de sessão único** no menu de navegação do lado esquerdo da aplicação.

8. Sob o **atributos de utilizador** secção, selecione o identificador exclusivo para os seus utilizadores no **identificador de utilizador** lista pendente. A opção selecionada tem de corresponder ao valor esperado na aplicação para autenticar o utilizador.

   >[!NOTE]
   >O Azure AD selecionar o formato para o atributo NameID (identificador de utilizador) com base no valor selecionado ou o formato de pedido com o aplicativo em AuthRequest o SAML. Para obter mais informações, visite o artigo [protocolo SAML de início de sessão único](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) na secção NameIDPolicy.
   >
   >

9. Para adicionar os atributos de utilizador, clique em **ver e editar todos os outros atributos de utilizador** para editar os atributos a serem enviados para a aplicação no SAML token, quando os utilizadores iniciam sessão.

   Para adicionar um atributo:

   1. clique em **adicionar atributo**. Introduza o **Name** e selecione o **valor** na lista pendente.

   Clique em 2 **guardar.** O atributo novo é apresentado na tabela.

### <a name="download-the-azure-ad-metadata-or-certificate"></a>Transferir os metadados do Azure AD ou o certificado

Para transferir os metadados da aplicação ou o certificado do Azure AD, siga os passos abaixo:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Coadministrador.**

2. Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione a aplicação tiver configurado o início de sessão único.

7. Assim que o aplicativo é carregado, clique nas **início de sessão único** no menu de navegação do lado esquerdo da aplicação.

8. Aceda a **certificado de assinatura SAML** secção, em seguida, clique em **transferir** valor da coluna. Consoante o que o aplicativo requer configurar início de sessão único, verá a opção para transferir o XML de metadados ou o certificado.

   O Azure AD não fornece um URL para obter os metadados. Os metadados só podem ser obtidos como um arquivo XML.

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Como configurar a palavra-passe início de sessão único para uma aplicação da galeria do Azure AD

Para configurar uma aplicação da galeria do Azure AD, que tem de:

-   Adicionar uma aplicação a partir da galeria do Azure AD

-   Configurar a aplicação para a palavra-passe início de sessão único

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Adicionar uma aplicação a partir da galeria do Azure AD

Para adicionar uma aplicação da galeria do Azure AD, siga os passos abaixo:

1.  Abra o [portal do Azure](https://portal.azure.com) e inicie sessão como um **Administrador Global** ou **coadministrador**

2.  Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4.  Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5.  Clique nas **Add** botão no canto superior direito a **aplicações empresariais** painel.

6.  Na **introduza um nome** caixa de texto da **adicionar a partir da galeria** secção, escreva o nome da aplicação

7.  Selecione a aplicação que pretende configurar para início de sessão único

8.  Antes de adicionar a aplicação, pode alterar o nome a partir da **nome** caixa de texto.

9.  Clique em **adicionar** botão, para adicionar a aplicação.

Após um curto período, pode ver o painel de configuração do aplicativo.

### <a name="configure-the-application-for-password-single-sign-on"></a>Configurar a aplicação para a palavra-passe início de sessão único

Para configurar o início de sessão único para uma aplicação, siga os passos abaixo:

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

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Como configurar a palavra-passe início de sessão único para uma aplicação de externas à Galeria

Para configurar uma aplicação da galeria do Azure AD, que tem de:

-   [Adicionar uma aplicação de externas à Galeria](#add-a-non-gallery-application)

-   [Configurar a aplicação para a palavra-passe início de sessão único](#configure-the-application-for-password-single-sign-on)

### <a name="add-a-non-gallery-application"></a>Adicionar uma aplicação de externas à Galeria

Para adicionar uma aplicação da galeria do Azure AD, siga os passos abaixo:

1.  Abra o [portal do Azure](https://portal.azure.com) e inicie sessão como um **Administrador Global** ou **coadministrador**

2.  Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4.  Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5.  Clique nas **Add** botão no canto superior direito a **aplicações empresariais** painel.

6.  Clique em **aplicação da Galeria não.**

7.  Introduza o nome da sua aplicação no **nome** caixa de texto. Selecione **adicionar.**

Após um curto período, poderá ver o painel de configuração do aplicativo.

### <a name="configure-the-application-for-password-single-sign-on"></a>Configurar a aplicação para a palavra-passe início de sessão único

Para configurar o início de sessão único para uma aplicação, siga os passos abaixo:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Coadministrador.**

2. Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione a aplicação que pretende configurar o início de sessão único.

7. Assim que o aplicativo é carregado, clique nas **início de sessão único** no menu de navegação do lado esquerdo da aplicação.

8. Selecione o modo **baseado em palavra-passe de início de sessão.**

9. Introduza o **URL de início de sessão**. Este é o URL em que os utilizadores introduzirem o respetivo nome de utilizador e palavra-passe para iniciar sessão. Certifique-se de que os campos de início de sessão são visíveis no URL.

10. Atribua utilizadores à aplicação.

11. Além disso, também pode fornecer as credenciais em nome do utilizador ao selecionar as linhas dos utilizadores e clicar em **as credenciais de atualização** e introduzindo o nome de utilizador e palavra-passe em nome dos utilizadores. Caso contrário, ser pedido aos utilizadores para introduzir as credenciais durante a inicialização propriamente ditas.

## <a name="how-to-assign-a-user-to-an-application-directly"></a>Como atribuir um utilizador a uma aplicação diretamente

Para atribuir diretamente um ou mais utilizadores a uma aplicação, siga os passos abaixo:

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

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Se efetuar estes passos de resolução de problemas não o resolve o problema

Abra um pedido de suporte com as seguintes informações se estiver disponível:

-   ID de correlação de erro

-   UPN (endereço de e-mail do utilizador)

-   TenantID

-   Tipo de navegador

-   Fuso horário e tempo/período de tempo durante o erro ocorre

-   Rastreios do fiddler

## <a name="next-steps"></a>Passos Seguintes
[Fornecer início de sessão único às suas aplicações com o Proxy de aplicações](application-proxy-configure-single-sign-on-with-kcd.md)

