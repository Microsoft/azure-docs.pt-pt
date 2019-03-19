---
title: Uma aplicação atribuída não é apresentada no painel de acesso | Documentos da Microsoft
description: Resolver problemas de por que uma aplicação não é apresentada no painel de acesso
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
ms.topic: article
ms.date: 09/09/2018
ms.author: celested
ms.reviwer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9bac0e78c53657cda1716dd8ab6ffd0e2453290f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58117426"
---
# <a name="an-assigned-application-is-not-appearing-on-the-access-panel"></a>Uma aplicação atribuída não é apresentada no painel de acesso

O painel de acesso é um portal baseado na web, que permite que um utilizador com uma conta profissional ou escolar no Azure Active Directory (Azure AD) para ver e iniciar os aplicativos baseados na nuvem que o administrador do Azure AD lhe concedeu-lhes acesso. Esses aplicativos são configurados em nome do utilizador no portal do Azure AD. A aplicação tem de ser configurada corretamente e atribuída ao usuário ou um grupo que o utilizador é membro de para ver a aplicação no painel de acesso.

O tipo de aplicações pode estar a ver um utilizador se enquadram nas seguintes categorias:

-   Aplicativos do Office 365

-   Aplicativos Microsoft e de terceiros configurados com o SSO baseado em Federação

-   Aplicativos de SSO de palavra-passe

-   Aplicações com soluções existentes de SSO

## <a name="general-issues-to-check-first"></a>Problemas gerais para verificar primeiro

-   Se uma aplicação acabou de ser adicionada a um utilizador, tente iniciar sessão e reduzir novamente no painel de acesso do utilizador após alguns minutos para ver se o aplicativo é adicionado.

-   Se apenas tiver sido removida uma licença de um utilizador ou grupo, o utilizador é que membro de isto poderá demorar muito tempo, dependendo do tamanho e a complexidade do grupo para que as alterações sejam feitas. Permita tempo adicional antes de iniciar sessão no painel de acesso.

## <a name="problems-related-to-application-configuration"></a>Problemas relacionados com a configuração da aplicação

Uma aplicação não pode ser apresentada no painel de acesso de um utilizador, porque não está configurado corretamente. Seguem-se algumas formas pode resolver problemas relacionados com a configuração da aplicação:

-   [Como configurar o início de sessão único federado para uma aplicação da galeria do Azure AD](#how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application)

-   [Como configurar o início de sessão único federado para uma aplicação de externas à Galeria](#how-to-configure-federated-single-sign-on-for-a-non-gallery-application)

-   [Como configurar uma palavra-passe único início de sessão na aplicação para uma aplicação da galeria do Azure AD](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

-   [Como configurar uma palavra-passe único início de sessão na aplicação para uma aplicação de externas à Galeria](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

### <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Como configurar o início de sessão único federado para uma aplicação da galeria do Azure AD

Todas as aplicações na galeria do Azure AD, ativada com capacidade de Enterprise Single Sign-On tem um tutorial passo a passo disponível. Pode aceder a [lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) para uma orientação passo a passo de detalhe.

Para configurar uma aplicação da galeria do Azure AD, que tem de:

-   [Adicionar uma aplicação a partir da galeria do Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Configurar valores de metadados da aplicação no Azure AD (URL de resposta de URL, identificador, início de sessão)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Selecione o identificador de utilizador e adicionar atributos de utilizador sejam enviados para a aplicação](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Obter metadados do Azure AD e o certificado](#download-the-azure-ad-metadata-or-certificate)

-   [Configurar valores de metadados do Azure AD no aplicativo (início de sessão no URL, o emissor, o URL de fim de sessão e o certificado)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Adicionar uma aplicação a partir da galeria do Azure AD

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

Após um curto período, poderá ver o painel de configuração do aplicativo.

#### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Configurar o início de sessão único para uma aplicação da galeria do Azure AD

Para configurar o início de sessão único para uma aplicação, siga os passos abaixo:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Coadministrador.**

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

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Selecione o identificador de utilizador e adicionar atributos de utilizador sejam enviados para a aplicação

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
   >O Azure AD seleciona o formato para o atributo NameID (identificador de utilizador) com base no valor selecionado ou o formato de pedido com o aplicativo em AuthRequest o SAML. Para obter mais informações, visite o artigo [protocolo SAML de início de sessão único](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) na secção NameIDPolicy.
   >
   >

9. Para adicionar os atributos de utilizador, clique em **ver e editar todos os outros atributos de utilizador** para editar os atributos a serem enviados para a aplicação no SAML token, quando os utilizadores iniciam sessão.

   Para adicionar um atributo:

   1. Clique em **adicionar atributo**. Introduza o **Name** e selecione o **valor** na lista pendente.

   2. Clique em **guardar.** Verá o novo atributo na tabela.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Transferir os metadados do Azure AD ou o certificado

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

### <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Como configurar o início de sessão único federado para uma aplicação de externas à Galeria

Para configurar uma aplicação de externas à galeria, tem de ter o Azure AD premium e a aplicação suporta SAML 2.0. Para obter mais informações sobre as versões do AD do Azure, visite [preços do Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

-   [Configurar valores de metadados da aplicação no Azure AD (URL de resposta de URL, identificador, início de sessão)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Selecione o identificador de utilizador e adicionar atributos de utilizador sejam enviados para a aplicação](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Obter metadados do Azure AD e o certificado](#download-the-azure-ad-metadata-or-certificate)

-   [Configurar valores de metadados do Azure AD no aplicativo (início de sessão no URL, o emissor, o URL de fim de sessão e o certificado)](#configure-the-application-for-password-single-sign-on-1)

#### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>Configurar valores de metadados da aplicação no Azure AD (URL de resposta de URL, identificador, início de sessão)

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

10. Selecione **baseado em SAML logon** no **modo** lista pendente.

11. Introduza os valores necessários no **domínio e URLs.** Deve obter estes valores do fabricante da aplicação.

    1. Para configurar a aplicação como SSO iniciado por IdP, introduza o URL de resposta e o identificador.

    2.  **Opcional:** Para configurar a aplicação como SSO iniciado por SP, o URL de início de sessão é um valor obrigatório.

12. Na **atributos de utilizador**, selecione o identificador exclusivo para os seus utilizadores no **identificador de utilizador** lista pendente.

13. **Opcional:** clique em **ver e editar todos os outros atributos de utilizador** para editar os atributos a serem enviados para a aplicação no SAML token, quando os utilizadores iniciam sessão.

    Para adicionar um atributo:

    1. Clique em **adicionar atributo**. Introduza o **Name** e selecione o **valor** na lista pendente.

    2. Clique em **guardar.** O atributo novo é apresentado na tabela.

14. Clique em **configurar &lt;nome da aplicação&gt;**  para aceda à documentação sobre como configurar o início de sessão único no aplicativo. Além disso, terá de URLs do Azure AD e os certificados necessários para a aplicação.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Selecione o identificador de utilizador e adicionar atributos de utilizador sejam enviados para a aplicação

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
   >O Azure AD seleciona o formato para o atributo NameID (identificador de utilizador) com base no valor selecionado ou o formato de pedido com o aplicativo em AuthRequest o SAML. Para obter mais informações, visite o artigo [protocolo SAML de início de sessão único](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) na secção NameIDPolicy.
   >
   >

9. Para adicionar os atributos de utilizador, clique em **ver e editar todos os outros atributos de utilizador** para editar os atributos a serem enviados para a aplicação no SAML token, quando os utilizadores iniciam sessão.

   Para adicionar um atributo:

   1. Clique em **adicionar atributo**. Introduza o **Name** e selecione o **valor** na lista pendente.

   2. Clique em **guardar.** O atributo novo é apresentado na tabela.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Transferir os metadados do Azure AD ou o certificado

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

### <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Como configurar a palavra-passe início de sessão único para uma aplicação da galeria do Azure AD

Para configurar uma aplicação da galeria do Azure AD, que tem de:

-   [Adicionar uma aplicação a partir da galeria do Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Configurar a aplicação para a palavra-passe início de sessão único](#configure-the-application-for-password-single-sign-on)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Adicionar uma aplicação a partir da galeria do Azure AD

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

#### <a name="configure-the-application-for-password-single-sign-on"></a>Configurar a aplicação para a palavra-passe início de sessão único

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

9. [Atribuir utilizadores à aplicação](#how-to-assign-a-user-to-an-application-directly).

10. Além disso, também pode fornecer as credenciais em nome do utilizador ao selecionar as linhas dos utilizadores e clicar em **as credenciais de atualização** e introduzindo o nome de utilizador e palavra-passe em nome dos utilizadores. Caso contrário, ser pedido aos utilizadores para introduzir as credenciais durante a inicialização propriamente ditas.

### <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Como configurar a palavra-passe início de sessão único para uma aplicação de externas à Galeria

Para configurar uma aplicação da galeria do Azure AD, que tem de:

-   [Adicionar uma aplicação de externas à Galeria](#add-a-non-gallery-application)

-   [Configurar a aplicação para a palavra-passe início de sessão único](#configure-the-application-for-password-single-sign-on)

#### <a name="add-a-non-gallery-application"></a>Adicionar uma aplicação de externas à Galeria

Para adicionar uma aplicação da galeria do Azure AD, siga os passos abaixo:

1.  Abra o [portal do Azure](https://portal.azure.com) e inicie sessão como um **Administrador Global** ou **coadministrador**.

2.  Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4.  Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5.  Clique nas **Add** botão no canto superior direito a **aplicações empresariais** painel.

6.  Clique em **aplicação da Galeria não.**

7.  Introduza o nome da sua aplicação no **nome** caixa de texto. Selecione **adicionar.**

Após um curto período, poderá ver o painel de configuração do aplicativo.

#### <a name="configure-the-application-for-password-single-sign-on"></a>Configurar a aplicação para a palavra-passe início de sessão único

Para configurar o início de sessão único para uma aplicação, siga os passos abaixo:

1.  Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Coadministrador.**

2.  Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4.  Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5.  Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

    1.  Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6.  Selecione a aplicação que pretende configurar o início de sessão único.

7.  Assim que o aplicativo é carregado, clique nas **início de sessão único** no menu de navegação do lado esquerdo da aplicação.

8.  Selecione o modo **baseado em palavra-passe de início de sessão.**

9.  Introduza o **URL de início de sessão**. Este é o URL em que os utilizadores introduzirem o respetivo nome de utilizador e palavra-passe para iniciar sessão no. Certifique-se de que os campos de início de sessão são visíveis no URL.

10. [Atribuir utilizadores à aplicação](#how-to-assign-a-user-to-an-application-directly).

11. Além disso, também pode fornecer as credenciais em nome do utilizador ao selecionar as linhas dos utilizadores e clicar em **as credenciais de atualização** e introduzindo o nome de utilizador e palavra-passe em nome dos utilizadores. Caso contrário, ser pedido aos utilizadores para introduzir as credenciais durante a inicialização propriamente ditas.

## <a name="problems-related-to-assigning-applications-to-users"></a>Problemas relacionados com a atribuição de aplicações para utilizadores

Um utilizador pode não estar a ver uma aplicação no seu painel de acesso porque não estão atribuídos à aplicação. Seguem-se algumas formas de verificar:

-   [Verificar se um utilizador é atribuído à aplicação](#check-if-a-user-is-assigned-to-the-application)

-   [Como atribuir um utilizador a uma aplicação diretamente](#how-to-assign-a-user-to-an-application-directly)

-   [Verificar se um utilizador é atribuído a uma licença relacionadas com a aplicação](#check-if-a-user-is-under-a-license-related-to-the-application)

-   [Como atribuir uma licença a um utilizador](#how-to-assign-a-user-a-license)

### <a name="check-if-a-user-is-assigned-to-the-application"></a>Verificar se um utilizador é atribuído à aplicação

Para verificar se um utilizador é atribuído à aplicação, siga os passos abaixo:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2. Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

6. **Pesquisa** para o nome do aplicativo em questão.

7. Clique em **utilizadores e grupos**.

8. Verifique se o utilizador é atribuído à aplicação.

   * Se não siga os passos em "Como atribuir um utilizador a uma aplicação diretamente" para fazer isso.

### <a name="how-to-assign-a-user-to-an-application-directly"></a>Como atribuir um utilizador a uma aplicação diretamente

Para atribuir diretamente um ou mais utilizadores a uma aplicação, siga os passos abaixo:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global**.

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

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Verificar se um utilizador é sob uma licença relacionadas com a aplicação

Para verificar as licenças atribuídas de um utilizador, siga os passos abaixo:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2. Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. Clique em **utilizadores e grupos** no menu de navegação.

5. Clique em **todos os utilizadores**.

6. **Pesquisa** para o utilizador que está interessado e **clique na linha** para selecionar.

7. Clique em **licenças** ver quais licenças atualmente o utilizador tenha atribuído.

   * Se o utilizador é atribuído a uma licença do Office isso permite que aplicativos do Office de terceiros primeiro a aparecer no painel de acesso do utilizador.

### <a name="how-to-assign-a-user-a-license"></a>Como atribuir uma licença de um utilizador 

Para atribuir uma licença a um utilizador, siga os passos abaixo:

1.  Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4.  Clique em **utilizadores e grupos** no menu de navegação.

5.  Clique em **todos os utilizadores**.

6.  **Pesquisa** para o utilizador que está interessado e **clique na linha** para selecionar.

7.  Clique em **licenças** ver quais licenças atualmente o utilizador tenha atribuído.

8.  Clique nas **atribuir** botão.

9.  Selecione **um ou mais produtos** na lista de produtos disponíveis.

10. **Opcional** clique a **opções de atribuição** item para atribuir especificamente com produtos. Clique em **Ok** quando for concluído.

11. Clique nas **atribuir** botão para atribuir essas licenças para este utilizador.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Problemas relacionados com a atribuição de aplicações a grupos

Um utilizador pode estar a ver uma aplicação no seu painel de acesso porque fazem parte de um grupo que tenha sido atribuído a aplicação. Seguem-se algumas formas de verificar:

-   [Verifique as associações de grupo de um utilizador](#check-a-users-group-memberships)

-   [Como atribuir diretamente uma aplicação a um grupo](#how-to-assign-an-application-to-a-group-directly)

-   [Verificar se um utilizador faz parte do grupo atribuído a uma licença](#check-if-a-user-is-part-of-group-assigned-to-a-license)

-   [Como atribuir uma licença a um grupo](#how-to-assign-a-license-to-a-group)

### <a name="check-a-users-group-memberships"></a>Verifique as associações de grupo de um utilizador

Para verificar uma associação de grupo, siga os passos abaixo:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2. Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. Clique em **utilizadores e grupos** no menu de navegação.

5. Clique em **todos os utilizadores**.

6. **Pesquisa** para o utilizador que está interessado e **clique na linha** para selecionar.

7. Clique em **grupos**.

8. Verifique se o utilizador faz parte de um grupo atribuída à aplicação.

   * Se pretender remover o utilizador do grupo **clique na linha** da eliminação de grupo e selecione.

### <a name="how-to-assign-an-application-to-a-group-directly"></a>Como atribuir diretamente uma aplicação a um grupo

Para atribuir um ou mais grupos diretamente a uma aplicação, siga os passos abaixo:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global**.

2. Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione a aplicação que pretende atribuir um utilizador a partir da lista.

7. Assim que o aplicativo é carregado, clique em **utilizadores e grupos** no menu de navegação do lado esquerdo da aplicação.

8. Clique nas **Add** botão na parte superior do **utilizadores e grupos** lista para abrir o **adicionar atribuição** painel.

9. Clique nas **utilizadores e grupos** Seletor da **adicionar atribuição** painel.

10. Escreva o **nome do grupo completo** do grupo estiver interessado em atribuir para o **pesquisar por nome ou endereço de e-mail** caixa de pesquisa.

11. Paire o rato sobre o **grupo** na lista para revelar uma **caixa de verificação**. Clique na caixa de verificação junto a fotografia do perfil ou à logótipo para adicionar o utilizador para o grupo de **selecionados** lista.

12. **Opcional:** Se quiser **adicionar mais de um grupo**, tipo em outro **nome do grupo completo** no **procurar por nome ou endereço de e-mail** caixa de pesquisa e clique na caixa de verificação para adicionar este grupo para o **selecionados** lista.

13. Quando tiver terminado de selecionar os grupos, clique nas **selecione** botão para adicioná-los à lista de utilizadores e grupos que devem ser atribuídos à aplicação.

14. **Opcional:** clique a **selecionar função** Seletor no **adicionar atribuição** painel para selecionar uma função para atribuir aos grupos que selecionou.

15. Clique nas **atribuir** botão para atribuir a aplicação aos grupos selecionados.

Após um curto período, os utilizadores que selecionou ser capaz de iniciar estas aplicações no painel de acesso.

### <a name="check-if-a-user-is-part-of-group-assigned-to-a-license"></a>Verificar se um utilizador faz parte do grupo atribuído a uma licença

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2. Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. Clique em **utilizadores e grupos** no menu de navegação.

5. Clique em **todos os utilizadores**.

6. **Pesquisa** para o utilizador que está interessado e **clique na linha** para selecionar.

7. Clique em **grupos**.

8. Clique na linha de um grupo específico.

9. Clique em **licenças** ver que as licenças do grupo atribuiu a ele.

   * Se o grupo for atribuído a uma licença do Office, que isso pode permitir determinados aplicativos do Office de terceiros primeiro a aparecer no painel de acesso do utilizador.

### <a name="how-to-assign-a-license-to-a-group"></a>Como atribuir uma licença a um grupo

Para atribuir uma licença a um grupo, siga os passos abaixo:

1.  Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4.  Clique em **utilizadores e grupos** no menu de navegação.

5.  Clique em **todos os grupos**.

6.  **Pesquisa** para o grupo de interesse e **clique na linha** para selecionar.

7.  Clique em **licenças** ver quais licenças atualmente o grupo tiver atribuído.

8.  Clique nas **atribuir** botão.

9.  Selecione **um ou mais produtos** na lista de produtos disponíveis.

10. **Opcional** clique a **opções de atribuição** item para atribuir especificamente com produtos. Clique em **Ok** quando for concluído.

11. Clique nas **atribuir** botão para atribuir essas licenças para este grupo. Isto pode demorar muito tempo, dependendo do tamanho e a complexidade do grupo.

>[!NOTE]
>Para fazer isso com mais rapidez, considere atribuir temporariamente uma licença para o usuário diretamente. 
>
>

## <a name="next-steps"></a>Passos Seguintes
[Adicionar novos utilizadores ao Azure Active Directory](./../fundamentals/add-users-azure-active-directory.md)

