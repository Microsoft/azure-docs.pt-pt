---
title: 'Tutorial: Integração do Azure Active Directory com o DocuSign | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o DocuSign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 5c7d6116ed2925e57f094a67f27a11f9e2d61831
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499251"
---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Tutorial: Integração do Azure Active Directory com o DocuSign

Neste tutorial, você aprenderá a integrar o DocuSign com o Azure Active Directory (Azure AD).
A integração do DocuSign ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao DocuSign.
* Você pode permitir que seus usuários sejam conectados automaticamente ao DocuSign (logon único) com suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao DocuSign, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura habilitada para logon único do DocuSign

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O DocuSign dá suporte ao SSO iniciado por **SP**

* O DocuSign dá suporte ao [provisionamento automático de usuário](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial)

## <a name="adding-docusign-from-the-gallery"></a>Adicionando o DocuSign da Galeria

Para configurar a integração do DocuSign ao Azure AD, você precisará adicionar o DocuSign da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o DocuSign da galeria, execute as seguintes etapas:**

1. Na **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **DocuSign**, selecione **DocuSign** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![DocuSign na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta seção, você configurará e testará o logon único do Azure AD com o DocuSign, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no DocuSign.

Para configurar e testar o logon único do Azure AD com o DocuSign, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o logon único do DocuSign](#configure-docusign-single-sign-on)** – para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar usuário de teste do DocuSign](#create-docusign-test-user)** – para ter um equivalente de Brenda Simon no DocuSign que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o DocuSign, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **DocuSign** , selecione **logon único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-** enalimentado para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do DocuSign](common/sp-identifier.png)

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão:`https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. Na caixa de texto **identificador (ID da entidade)** , digite uma URL usando o seguinte padrão:`https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de logon e o identificador reais que são explicados posteriormente na seção **Exibir pontos de extremidade 2,0 do SAML** no tutorial.

5. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **certificado (Base64)** das opções especificadas de acordo com seu requisito e salve-o no computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Na seção **Configurar DocuSign** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-docusign-single-sign-on"></a>Configurar o logon único do DocuSign

1. Em uma janela diferente do navegador da Web, entre no **portal de administração do DocuSign** como um administrador.

2. No canto superior direito da página, clique no **logotipo** do perfil e, em seguida, clique em **ir para administrador**.
  
    ![A configurar o início de sessão único][51]

3. Na página soluções de domínio, clique em **domínios**

    ![A configurar o início de sessão único][50]

4. Na seção **domínios** , clique em **declarar domínio**.

    ![A configurar o início de sessão único][52]

5. Na caixa de diálogo **declarar um domínio** , na TextBox **nome de domínio** , digite o domínio da empresa e clique em **reivindicação**. Verifique se você verificou o domínio e se o status está ativo.

    ![A configurar o início de sessão único][53]

6. Na página soluções de domínio, clique em **provedores de identidade**.
  
    ![A configurar o início de sessão único][54]

7. Na seção **provedores de identidade** , clique em **Adicionar provedor de identidade**. 

    ![A configurar o início de sessão único][55]

8. Na página **configurações do provedor de identidade** , execute as seguintes etapas:

    ![A configurar o início de sessão único][56]

    a. Na caixa de texto **nome** , digite um nome exclusivo para a sua configuração. Não use espaços.

    b. Na **caixa de texto emissor do provedor de identidade**, Cole o valor do identificador do **Azure ad**que você copiou do portal do Azure.

    c. Na caixa de texto **URL de logon do provedor de identidade** , Cole o valor da URL de **logon**copiado de portal do Azure.

    d. Na caixa de texto **URL de logout do provedor de identidade** , Cole o valor da URL de **logout**copiado de portal do Azure.

    e. Selecione **assinar solicitação Authn**.

    f. **Para enviar solicitação Authn por**, selecione **post**.

    g. Como **Enviar solicitação de logout por**, selecione **obter**.

    h. Na seção **mapeamento de atributo personalizado** , clique em **Adicionar novo mapeamento**.

    ![A configurar o início de sessão único][62]

    i. Escolha o campo que você deseja mapear com a declaração do Azure AD. Neste exemplo, a Declaração **EmailAddress** é mapeada com o valor de **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** . É o nome de declaração padrão do Azure AD para declaração de email e, em seguida, clica em **salvar**.

    ![A configurar o início de sessão único][57]

    > [!NOTE]
    > Use o **identificador de usuário** apropriado para mapear o usuário do Azure ad para o mapeamento de usuário do DocuSign. Selecione o campo apropriado e insira o valor apropriado com base nas configurações da sua organização.

    j. Na seção **certificados do provedor de identidade** , clique em **Adicionar certificado**e, em seguida, carregue o certificado que você baixou do portal do Azure AD e clique em **salvar**.

    ![A configurar o início de sessão único][58]

    k. Na seção **provedores de identidade** , clique em **ações**e em **pontos de extremidade**.

    ![A configurar o início de sessão único][59]

    l. Na seção **Exibir pontos de extremidade SAML 2,0** no **portal de administração do DocuSign**, execute as seguintes etapas:

    ![A configurar o início de sessão único][60]

    * Copie a **URL do emissor do provedor de serviços**e cole-a na caixa de texto **identificador** na seção **configuração básica do SAML** no portal do Azure.

    * Copie a **URL de logon do provedor de serviços**e cole-a na caixa de texto **URL de logon** na seção **configuração básica do SAML** no portal do Azure.

    * Clique em **fechar**

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Botão novo usuário](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. No campo **nome** , insira **brendafernandes**.
  
    b. No campo **nome de usuário** , brittasimon@yourcompanydomain.extensiondigite. Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao DocuSign.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **DocuSign**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **DocuSign**.

    ![O link do DocuSign na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-docusign-test-user"></a>Criar usuário de teste do DocuSign

Nesta seção, um usuário chamado Brenda Simon é criado em DocuSign. O DocuSign dá suporte ao provisionamento de usuário just-in-time, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir no DocuSign, um novo será criado após a autenticação.

>[!Note]
>Se você precisar criar um usuário manualmente, entre em contato com a [equipe de suporte do DocuSign](https://support.docusign.com/).

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do DocuSign no painel de acesso, você deverá ser conectado automaticamente ao DocuSign para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[50]: ./media/docusign-tutorial/tutorial_docusign_18.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[62]: ./media/docusign-tutorial/tutorial_docusign_30.png
