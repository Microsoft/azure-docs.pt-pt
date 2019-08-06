---
title: 'Tutorial: Integração de Azure Active Directory com o conector de metaredes | Microsoft Docs'
description: Saiba como configurar o logon único entre o conector do Azure Active Directory e do metanetworks.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4ae5f30d-113b-4261-b474-47ffbac08bf7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: 5ef49cb2e245e4af7b5d7f7d250194761cf0b375
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68826361"
---
# <a name="tutorial-azure-active-directory-integration-with-meta-networks-connector"></a>Tutorial: Integração de Azure Active Directory com o conector de metaredes

Neste tutorial, você aprenderá a integrar o conector de metaredes ao Azure Active Directory (Azure AD).
A integração do conector de metaredes ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao conector meta Networks.
* Você pode permitir que os usuários sejam automaticamente conectados ao conector de metaredes (logon único) com suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao conector de metaredes, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do conector metanetworks

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O conector de metaredes dá suporte ao **SP** iniciado pelo
 
* O conector de metaredes dá suporte ao provisionamento **de usuário just-in-time**

## <a name="adding-meta-networks-connector-from-the-gallery"></a>Adicionando o conector meta Networks da Galeria

Para configurar a integração do conector metanetworks ao Azure AD, você precisa adicionar o conector meta Networks da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o conector de metaredes por meio da galeria, execute as seguintes etapas:**

1. Na **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **conector**de metaredes, selecione **conector** de metaredes no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Conector de metaredes na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta seção, você configurará e testará o logon único do Azure AD com o conector de metaredes com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no conector de metaredes.

Para configurar e testar o logon único do Azure AD com o conector de metaredes, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o logon único do conector](#configure-meta-networks-connector-single-sign-on)** metanetworks-para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar usuário de teste do conector](#create-meta-networks-connector-test-user)** de metaredes – para ter um equivalente de Brenda Simon no conector de metaredes que esteja vinculado à representação de usuário do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o conector de metaredes, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **conector** de metaredes, selecione **logon único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-** enalimentado para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do conector do meta Networks](common/idp-intiated.png)

    a. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão:`https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/saml/metadata`

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão:`https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/sso/saml`

5. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    ![Informações de logon único de domínio e URLs do conector do meta Networks](common/both-advanced-urls.png)

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão:`https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/login`

    b. Na caixa de texto **estado** de retransmissão, digite uma URL usando o seguinte padrão:`https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/#/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador, a URL de resposta e a URL de logon reais são explicados posteriormente no tutorial.

6. O aplicativo conector de metaredes espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique em **Editar** ícone para abrir a caixa de diálogo **atributos de usuário** .

    ![image](common/edit-attribute.png)
    
7. Além disso, o aplicativo do conector de metaredes espera que mais alguns atributos sejam passados de volta na resposta SAML. Na seção **declarações do usuário** , na caixa de diálogo **atributos de usuário** , execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo:
    
    | Nome | Atributo de origem | Espaço de Nomes|
    | ---------------| --------------- | -------- |
    | FirstName | user.givenname | |
    | LastName | User. sobrenome | |
    | EmailAddress| user.mail| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | name | user.userprincipalname| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | telefone | User. telephoneNumber | |

    a. Clique em **Adicionar nova declaração** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **namespace** em branco.

    d. Selecione origem como **atributo**.

    e. Na lista **atributo de origem** , digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

8. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **certificado (Base64)** das opções especificadas de acordo com seu requisito e salve-o no computador.

    ![O link de download de certificado](common/certificatebase64.png)

9. Na seção **Configurar o conector** de metaredes, copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-meta-networks-connector-single-sign-on"></a>Configurar o logon único do conector metanetworks

1. Abra uma nova guia no navegador e faça logon na conta de administrador do conector de metaredes.
    
    > [!NOTE]
    > O conector de metaredes é um sistema seguro. Portanto, antes de acessar o portal, você precisa obter seu endereço IP público adicionado a uma lista de permissões em seu lado. Para obter seu endereço IP público, siga o link abaixo especificado [aqui](https://whatismyipaddress.com/). Envie seu endereço IP para a [equipe de suporte do cliente do conector](mailto:support@metanetworks.com) de metaredes para obter seu endereço IP adicionado a uma lista de permissões.
    
2. Vá para **administrador** e selecione **configurações**.
    
    ![Configurar o início de sessão único](./media/metanetworksconnector-tutorial/configure3.png)
    
3. Verifique se o **tráfego de Internet de log** e **Force VPN MFA** estão definidos como off.
    
    ![Configurar o início de sessão único](./media/metanetworksconnector-tutorial/configure1.png)
    
4. Vá para **administrador** e selecione **SAML**.
    
    ![Configurar o início de sessão único](./media/metanetworksconnector-tutorial/configure4.png)
    
5. Execute as seguintes etapas na página de **detalhes** :
    
    ![Configurar o início de sessão único](./media/metanetworksconnector-tutorial/configure2.png)
    
    a. Copie o valor da **URL de SSO** e cole-o na caixa de texto **URL de entrada** na seção **domínio e URLs do conector** de metaredes.
    
    b. Copie o valor da **URL do destinatário** e cole-o na caixa de texto **URL de resposta** na seção **domínio e URLs do conector** de metaredes.
    
    c. Copie o valor do **URI da audiência (ID da entidade do SP)** e cole-o na caixa de texto **identificador (ID da entidade)** na seção **domínio e URLs do conector** de metaredes.
    
    d. Habilitar o SAML
    
6. Na guia **geral** , execute as seguintes etapas:

    ![Configurar o início de sessão único](./media/metanetworksconnector-tutorial/configure5.png)

    a. Na **URL de logon único do provedor de identidade**, Cole o valor da URL de **logon** copiado do portal do Azure.

    b. No **emissor do provedor de identidade**, Cole o valor do identificador do **Azure ad** que você copiou do portal do Azure.

    c. Abra o certificado baixado de portal do Azure no bloco de notas, Cole-o na caixa de texto **certificado X. 509** .

    d. Habilite o **provisionamento just-in-time**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Botão novo usuário](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** , insira **BrittaSimon**.
  
    b. No campo **nome de usuário** , digite **brendafernandes\@yourcompanydomain. Extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao conector de metaredes.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **conector**de metaredes.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **conector**de metaredes.

    ![O link do conector de metaredes na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-meta-networks-connector-test-user"></a>Criar usuário de teste do conector meta Networks

Nesta seção, um usuário chamado Brenda Simon é criado no conector meta Networks. O conector de metaredes dá suporte ao provisionamento just-in-time, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir no conector de metaredes, um novo será criado quando você tentar acessar o conector de metaredes.

>[!Note]
>Se você precisar criar um usuário manualmente, entre em contato com a [equipe de suporte do cliente do conector](mailto:support@metanetworks.com)de metaredes.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco conector de metaredes no painel de acesso, você deverá ser conectado automaticamente ao conector de metaredes para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

