---
title: 'Tutorial: Integração do Active Directory do Azure com OrgChart agora | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e OrgChart agora.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 50a1522f-81de-4d14-9b6b-dd27bb1338a4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: d34b40195a5f8effb794f3fbefc7949740509e27
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57835668"
---
# <a name="tutorial-azure-active-directory-integration-with-orgchart-now"></a>Tutorial: Integração do Active Directory do Azure com OrgChart agora

Neste tutorial, saiba como integrar OrgChart agora com o Azure Active Directory (Azure AD).
Integrar OrgChart agora com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao OrgChart agora.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para OrgChart agora (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com OrgChart agora, precisa do seguinte:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Agora OrgChart logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta agora OrgChart **SP** e **IDP** iniciada SSO

## <a name="adding-orgchart-now-from-the-gallery"></a>Adicionando OrgChart agora da Galeria

Para configurar a integração do OrgChart agora para o Azure AD, terá de adicionar OrgChart agora a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar OrgChart agora a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **agora OrgChart**, selecione **OrgChart agora** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![OrgChart agora na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com OrgChart agora com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado OrgChart agora tem de ser estabelecida.

Para configurar e testar o Azure AD início de sessão único com OrgChart agora, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar OrgChart agora início de sessão único](#configure-orgchart-now-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste agora OrgChart](#create-orgchart-now-test-user)**  - para ter um equivalente da Eduarda Almeida OrgChart agora que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com OrgChart agora, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **OrgChart agora** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, executar o passo seguinte:

    ![OrgChart agora o domínio e URLs únicas início de sessão em informações](common/idp-identifier.png)

    Na **identificador** caixa de texto, escreva um URL:  `https://sso2.orgchartnow.com`

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![image](common/both-preintegrated-signon.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://sso2.orgchartnow.com/Shibboleth.sso/Login?entityID=<YourEntityID>&target=https://sso2.orgchartnow.com`

    > [!NOTE]
    > `<YourEntityID>` é o **do Azure AD identificador** copiados a partir do **configurar agora OrgChart** descrito na seção, mais tarde no tutorial.

6. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

7. Sobre o **configurar agora OrgChart** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-orgchart-now-single-sign-on"></a>Configurar agora OrgChart início de sessão único

Para configurar o início de sessão único num **agora OrgChart** lado, terá de enviar o transferido **XML de metadados de Federação** e adequadas copiados URLs a partir do portal do Azure para [equipa de suporte de OrgChart agora ](mailto:ocnsupport@officeworksoftware.com). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso a OrgChart Now.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **OrgChart agora**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **OrgChart agora**.

    ![A ligação de OrgChart agora na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-orgchart-now-test-user"></a>Criar utilizador de teste OrgChart agora

Para ativar a utilizadores do Azure AD iniciar sessão no OrgChart agora, tem de ser aprovisionados em OrgChart agora. 

1. OrgChart agora suporta o aprovisionamento de just-in-time, que está por predefinição, ativada. Um novo utilizador é criado durante uma tentativa de aceder OrgChart agora, se não existir ainda. A funcionalidade de aprovisionamento de utilizadores de just-in-time só irão criar um **só de leitura** utilizador quando um pedido SSO é proveniente de um IDP reconhecido e o e-mail na asserção de SAML não foi encontrado na lista de utilizadores. Para este automaticamente a funcionalidade de aprovisionamento tem de criar um grupo de acesso intitulado **gerais** OrgChart agora. Siga os passos abaixo para criar um grupo de acesso:

    a. Vá para o **gerir grupos** opção depois de clicar o **engrenagem** no canto superior direito da interface do Usuário.

    ![Grupos de OrgChart agora](./media/orgchartnow-tutorial/tutorial_orgchartnow_manage.png)    

    b. Selecione o **Add** ícone e o nome do grupo **gerais** , em seguida, clique em **OK**. 

    ![Adicionar OrgChart agora](./media/orgchartnow-tutorial/tutorial_orgchartnow_add.png)

    c. Selecione a pasta (s) que pretende que os usuários gerais ou só de leitura para poder aceder a:

    ![Pastas de OrgChart agora](./media/orgchartnow-tutorial/tutorial_orgchartnow_chart.png)

    d. **Bloqueio** as pastas para que apenas os utilizadores administradores podem modificá-las. Em seguida, prima **OK**.

    ![Bloqueio de OrgChart agora](./media/orgchartnow-tutorial/tutorial_orgchartnow_lock.png)

2. Para criar **administrador** os utilizadores e **leitura/escrita** utilizadores, tem de criar manualmente um utilizador para obter acesso ao respetivo nível de privilégios através de SSO. Para Aprovisionar uma conta de utilizador, execute os seguintes passos:

    a. Inicie sessão no OrgChart agora como um administrador de segurança.

    b.  Clique em **configurações** no canto superior direito canto e, em seguida, navegue até à **Manage Users**.

    ![Definições de OrgChart agora](./media/orgchartnow-tutorial/tutorial_orgchartnow_settings.png)

    c. Clique em **adicionar** e execute os seguintes passos:

    ![Gerir OrgChart agora](./media/orgchartnow-tutorial/tutorial_orgchartnow_manageusers.png)

    * Na **ID de utilizador** caixa de texto, introduza o ID de utilizador, como **brittasimon\@contoso.com**.

    * Na **endereço de E-Mail** texto, introduza o e-mail do utilizador, como **brittasimon\@contoso.com**.

    * Clique em **Adicionar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico OrgChart agora no painel de acesso, deve ser automaticamente sessão iniciada no OrgChart agora para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

