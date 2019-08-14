---
title: 'Tutorial: Integração do Azure Active Directory com o NetSuite | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o NetSuite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e462197bac854004aaf2d2f0f96e121ed081581a
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967244"
---
# <a name="tutorial-azure-active-directory-integration-with-netsuite"></a>Tutorial: Integração do Azure Active Directory com o NetSuite

Neste tutorial, você aprenderá a integrar o NetSuite ao Azure Active Directory (Azure AD).
A integração do NetSuite ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao NetSuite.
* Você pode permitir que seus usuários sejam conectados automaticamente ao NetSuite (logon único) com suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao NetSuite, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do NetSuite

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O NetSuite dá suporte ao SSO iniciado pelo **IDP**
* O NetSuite dá suporte ao provisionamento **de usuário just in time**
* O NetSuite dá suporte ao [provisionamento automatizado de usuários](NetSuite-provisioning-tutorial.md)

## <a name="adding-netsuite-from-the-gallery"></a>Adicionando o NetSuite da Galeria

Para configurar a integração do NetSuite ao Azure AD, você precisará adicionar o NetSuite da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o NetSuite da galeria, execute as seguintes etapas:**

1. Na **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **NetSuite**, selecione NetSuite no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![NetSuite na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta seção, você configurará e testará o logon único do Azure AD com o NetSuite, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do NetSuite.

Para configurar e testar o logon único do Azure AD com o NetSuite, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o logon único](#configure-netsuite-single-sign-on)** do NetSuite – para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar usuário de teste](#create-netsuite-test-user)** do NetSuite – para ter um equivalente de Brenda Simon no NetSuite que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o NetSuite, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do NetSuite, selecione **logon único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-** enalimentado para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do NetSuite](common/idp-reply.png)

    Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão:

    `https://<tenant-name>.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na1.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na2.NetSuite.com/saml2/acs`

    `https://<tenant-name>.sandbox.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na1.sandbox.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na2.sandbox.NetSuite.com/saml2/acs`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de resposta real. Contate a [equipe de suporte ao cliente](https://www.netsuite.com/portal/services/support-services/suitesupport.shtml) do NetSuite para obter o valor. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

5. O aplicativo NetSuite espera que as asserções SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos na seção **atributos de usuário** na página de integração de aplicativos. Na página **Configurar logon único com SAML** , clique no botão **Editar** para abrir a caixa de diálogo **atributos de usuário** .

    ![image](common/edit-attribute.png)

6. Na seção **declarações do usuário** , na caixa de diálogo **atributos de usuário** , edite as declarações usando o **ícone Editar** ou adicione as declarações usando **Adicionar nova declaração** para configurar o atributo de token SAML, conforme mostrado na imagem acima, e execute as seguintes etapas:
    
    | Nome | Atributo de origem | 
    | ---------------| --------------- |
    | conta  | `account id` |

    a. Clique em **Adicionar nova declaração** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **namespace** em branco.

    d. Selecione origem como **atributo**.

    e. Na lista **atributo de origem** , digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

    >[!NOTE]
    >O valor do atributo de conta não é real. Você atualizará esse valor, que é explicado posteriormente no tutorial.

4. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **XML de metadados** das opções especificadas de acordo com seu requisito e salve-o em seu computador.

    ![O link de download de certificado](common/metadataxml.png)

6. Na seção **Configurar** o NetSuite, copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-netsuite-single-sign-on"></a>Configurar o logon único do NetSuite

1. Abra uma nova guia no navegador e entre no site da empresa do NetSuite como um administrador.

2. Na barra de ferramentas na parte superior da página, clique em **configuração**, navegue até **empresa** e clique em **habilitar recursos**.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-setupsaml.png)

3. Na barra de ferramentas no meio da página, clique em **SuiteCloud**.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-suitecloud.png)

4. Na seção **gerenciar autenticação** , selecione **logon único do SAML** para habilitar a opção de logon único do SAML no NetSuite.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-ticksaml.png)

5. Na barra de ferramentas na parte superior da página, clique em **configuração**.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-setup.png)

6. Na lista **tarefas de instalação** , clique em **integração**.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-integration.png)

7. Na seção **gerenciar autenticação** , clique em **logon único do SAML**.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-saml.png)

8. Na página **instalação do SAML** , na seção **configuração** do NetSuite, execute as seguintes etapas:

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. Selecione o **método de autenticação primária**.

    b. Para o campo rotulado como **SAMLV2 metadados do provedor de identidade**, selecione **carregar arquivo de metadados IDP**. Em seguida, clique em **procurar** para carregar o arquivo de metadados que você baixou de portal do Azure.

    c. Clique em **Submit** (Submeter).

9. No NetSuite, clique em **configuração** e, em seguida, navegue até **empresa** e clique em **informações da empresa** no menu de navegação superior.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-com.png)

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-account-id.png)

    b. Na página **informações da empresa** , na coluna à direita, copie a **ID da conta**.

    c. Cole a **ID da conta** que você copiou da conta do NetSuite no campo **valor do atributo** no Azure AD. 

10. Antes que os usuários possam executar o logon único no NetSuite, eles devem primeiro receber as permissões apropriadas no NetSuite. Siga as instruções abaixo para atribuir essas permissões.

    a. No menu de navegação superior, clique em **configuração**.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-setup.png)

    b. No menu de navegação à esquerda, selecione **usuários/funções**e, em seguida, clique em **gerenciar funções**.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. Clique em **nova função**.

    d. Digite um **nome** para a nova função.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-new-role.png)

    e. Clique em **Guardar**.

    f. No menu na parte superior, clique em **permissões**. Em seguida, clique em **instalação**.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-sso.png)

    g. Selecione **logon único do SAML**e clique em **Adicionar**.

    h. Clique em **Guardar**.

    i. No menu de navegação superior, clique em **instalação**e em **Gerenciador de instalação**.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-setup.png)

    j. No menu de navegação à esquerda, selecione **usuários/funções**e clique em **gerenciar usuários**.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Selecione um usuário de teste. Em seguida, clique em **Editar** e navegue até a guia **acessar** .

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-edit-user.png)

    l. Na caixa de diálogo funções, atribua a função apropriada que você criou.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-add-role.png)

    m. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Botão novo usuário](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. No campo **nome** , insira **brendafernandes**.
  
    b. No campo **nome de usuário** , **digite\@brendafernandes yourcompanydomain. Extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao NetSuite.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione NetSuite.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e selecione **NetSuite**.

    ![O link do NetSuite na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-netsuite-test-user"></a>Criar usuário de teste do NetSuite

Nesta seção, um usuário chamado Brenda Simon é criado no NetSuite. O NetSuite dá suporte ao provisionamento de usuário just-in-time, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir no NetSuite, um novo será criado após a autenticação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do NetSuite no painel de acesso, você deverá ser conectado automaticamente ao NetSuite para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configurar provisionamento de usuário](NetSuite-provisioning-tutorial.md)

