---
title: 'Tutorial: Integração do Active Directory do Azure com o DocuSign | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o DocuSign.
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
ms.openlocfilehash: 607e5aded52375190878de6b48ffa4aa2ab49767
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67104090"
---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Tutorial: Integração do Active Directory do Azure com o DocuSign

Neste tutorial, saiba como integrar o DocuSign com o Azure Active Directory (Azure AD).
Integrar o DocuSign no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao DocuSign.
* Pode permitir que os utilizadores ser automaticamente assinado no DocuSign (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o DocuSign, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* DocuSign logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta DocuSign **SP** iniciada SSO

* Suporta DocuSign **Just In Time** aprovisionamento de utilizadores

## <a name="adding-docusign-from-the-gallery"></a>Adicionando DocuSign da Galeria

Para configurar a integração do DocuSign para o Azure AD, terá de adicionar DocuSign a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar DocuSign a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **DocuSign**, selecione **DocuSign** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![DocuSign na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o DocuSign com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no DocuSign deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o DocuSign, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o DocuSign Single Sign-On](#configure-docusign-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste do DocuSign](#create-docusign-test-user)**  - para ter um equivalente da Eduarda Almeida no DocuSign que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o DocuSign, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **DocuSign** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Domínio da DocuSign e URLs únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão real e o identificador que é explicado mais tarde **ver SAML 2.0 pontos finais** secção do tutorial.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar a DocuSign** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-docusign-single-sign-on"></a>Configurar o DocuSign Single Sign-On

1. Numa janela do browser web diferente, inicie sessão para sua **portal de administração do DocuSign** como administrador.

2. No canto superior direito da página, clique no perfil **logótipo** e, em seguida, clique em **vá para o administrador**.
  
    ![Configurar o início de sessão único][51]

3. Na página de soluções do domínio, clique em **domínios**

    ![Configurar o início de sessão único][50]

4. Sob o **domínios** secção, clique em **afirmação domínio**.

    ![Configurar o início de sessão único][52]

5. Na **um domínio de afirmação** caixa de diálogo, na **nome de domínio** caixa de texto, escreva o seu domínio da empresa e, em seguida, clique em **afirmação**. Certifique-se de que verificar o domínio e o estado estiver ativo.

    ![Configurar o início de sessão único][53]

6. Na página de soluções do domínio, clique em **fornecedores de identidade**.
  
    ![Configurar o início de sessão único][54]

7. Sob **fornecedores de identidade** secção, clique em **ADD IDENTITY PROVIDER**. 

    ![Configurar o início de sessão único][55]

8. Sobre o **definições do fornecedor de identidade** página, execute os seguintes passos:

    ![Configurar o início de sessão único][56]

    a. Na **nome** caixa de texto, escreva um nome exclusivo para a sua configuração. Não utilize espaços.

    b. Na **caixa de texto do emissor do fornecedor de identidade**, cole o valor de **do Azure AD identificador**, que copiou do portal do Azure.

    c. Na **URL de início de sessão do fornecedor de identidade** caixa de texto, cole o valor de **URL de início de sessão**, que copiou do portal do Azure.

    d. Na **URL de fim de sessão do fornecedor de identidade** caixa de texto, cole o valor de **URL de fim de sessão**, que copiou do portal do Azure.

    e. Selecione **pedido de início de sessão AuthN**.

    f. Como **AuthN enviar pedido pelo**, selecione **POST**.

    g. Como **pedido de fim de sessão de envio pelo**, selecione **obter**.

    h. Na **mapeamento de atributos personalizado** secção, clique em **adicionar novo mapeamento**.

    ![Configurar o início de sessão único][62]

    i. Escolha o campo que deseja mapear com afirmações do Azure AD. Neste exemplo, o **emailaddress** mapeada com o valor de afirmação **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** . É o nome de afirmação padrão do Azure AD para a afirmação de e-mail e clique em **guardar**.

    ![Configurar o início de sessão único][57]

    > [!NOTE]
    > Utilizar o adequado **identificador de utilizador** para mapear o utilizador do Azure AD para o mapeamento de utilizador do DocuSign. Selecione o campo apropriadas e introduza o valor apropriado com base nas suas definições de organização.

    j. Na **certificados de fornecedor de identidade** secção, clique em **ADD CERTIFICATE**e, em seguida, carregue o certificado que tenha transferido a partir do portal do Azure AD e clique em **guardar**.

    ![Configurar o início de sessão único][58]

    k. Na **fornecedores de identidade** secção, clique em **AÇÕES**e, em seguida, clique em **pontos finais**.

    ![Configurar o início de sessão único][59]

    l. Na **ver SAML 2.0 pontos finais** secção na **portal de administração do DocuSign**, execute os seguintes passos:

    ![Configurar o início de sessão único][60]

    * Cópia a **URL de emissor do fornecedor de serviço**e, em seguida, cole-o para o **identificador** caixa de texto no **configuração básica de SAML** secção no portal do Azure.

    * Cópia a **URL de início de sessão do fornecedor de serviço**e, em seguida, cole-o para o **URL de início de sessão** caixa de texto no **configuração básica de SAML** secção no portal do Azure.

    * Clique em **fechar**

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo brittasimon@yourcompanydomain.extension. Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso a DocuSign.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **DocuSign**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **DocuSign**.

    ![A ligação do DocuSign na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-docusign-test-user"></a>Criar utilizador de teste da DocuSign

Nesta secção, um usuário chamado Eduarda Almeida é criado no DocuSign. DocuSign suporta o aprovisionamento de utilizadores de just-in-time, que está ativado por predefinição. Não existe nenhum item de ação para nesta secção. Se um utilizador já não existir no DocuSign, é criado um novo após a autenticação.

>[!Note]
>Se precisar de criar manualmente um utilizador, contacte [equipa de suporte do DocuSign](https://support.docusign.com/).

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico do DocuSign no painel de acesso, deve ser automaticamente sessão iniciada no DocuSign para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

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
