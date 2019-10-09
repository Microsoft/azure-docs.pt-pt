---
title: 'Tutorial: Integração do Azure Active Directory com os sistemas pega Systems | Microsoft Docs'
description: Neste tutorial, você aprenderá a configurar o logon único entre os sistemas Azure Active Directory e pega Systems.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 31acf80f-1f4b-41f1-956f-a9fbae77ee69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: bd54466e054055ff84cd5bb2b28c5cc074ac0017
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026815"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Tutorial: Integração do Azure Active Directory com os sistemas pega Systems

Neste tutorial, você aprenderá a integrar o pega Systems Systems ao Azure Active Directory (Azure AD).

Essa integração fornece estes benefícios:

* Você pode usar o Azure AD para controlar quem tem acesso ao pega Systems Systems.
* Você pode permitir que seus usuários sejam conectados automaticamente ao pega Systems Systems (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [logon único para aplicativos no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao pega Systems Systems, você precisa ter:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, você poderá se inscrever para uma [avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).
* Uma assinatura de sistemas pega Systems com logon único habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* Os sistemas pega Systems dão suporte ao SSO iniciado por SP e iniciado pelo IdP.

## <a name="add-pega-systems-from-the-gallery"></a>Adicionar sistemas pega Systems da Galeria

Para configurar a integração do pega Systems Systems ao Azure AD, você precisará adicionar o pega Systems Systems da Galeria à sua lista de aplicativos SaaS gerenciados.

1. No [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **Azure Active Directory**:

    ![Selecione Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos empresariais** > **todos os aplicativos**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um aplicativo, selecione **novo aplicativo** na parte superior da janela:

    ![Selecionar novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **pega Systems Systems**. Selecione **sistemas pega Systems** nos resultados da pesquisa e, em seguida, selecione **Adicionar**.

     ![Resultados da pesquisa](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta seção, você configurará e testará o logon único do Azure AD com o pega Systems Systems usando um usuário de teste chamado Brenda Simon.
Para habilitar o logon único, você precisa estabelecer uma relação entre um usuário do Azure AD e o usuário correspondente no pega Systems Systems.

Para configurar e testar o logon único do Azure AD com o pega Systems Systems, você precisa concluir estas etapas:

1. **[Configure o logon único do Azure ad](#configure-azure-ad-single-sign-on)** para habilitar o recurso para seus usuários.
2. **[Configure o logon único do pega Systems Systems](#configure-pega-systems-single-sign-on)** no lado do aplicativo.
3. **[Crie um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD.
4. **[Atribua o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** para habilitar o logon único do Azure ad para o usuário.
5. **[Crie um usuário de teste do pega Systems Systems](#create-a-pega-systems-test-user)** que esteja vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o pega Systems Systems, execute estas etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **pega Systems Systems** , selecione **logon único**:

    ![Selecione início de sessão único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único:

    ![Selecionar um método de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , selecione o ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** :

    ![Ícone Editar](common/edit-urls.png)

4. Na caixa de diálogo **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo IDP, conclua as etapas a seguir.

    ![Caixa de diálogo configuração de SAML básica](common/idp-intiated.png)

    1. Na caixa **identificador** , insira uma URL neste padrão:

       `https://<customername>.pegacloud.io:443/prweb/sp/<instanceID>`

    1. Na caixa **URL de resposta** , insira uma URL neste padrão:

       `https://<customername>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. Se você quiser configurar o aplicativo no modo iniciado pelo SP, selecione **definir URLs adicionais** e conclua as etapas a seguir.

    ![Informações de logon único de domínio e URLs do pega Systems Systems](common/both-advanced-urls.png)

    1. Na caixa **URL de logon** , insira o valor da URL de logon.

    1. Na caixa **estado de retransmissão** , insira uma URL neste padrão: `https://<customername>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > Os valores fornecidos aqui são espaços reservados. Você precisa usar o identificador, a URL de resposta, a URL de logon e a URL do estado de retransmissão reais. Você pode obter os valores do identificador e da URL de resposta de um aplicativo pega Systems, conforme explicado posteriormente neste tutorial. Para obter o valor do estado de retransmissão, entre em contato com a [equipe de suporte do pega Systems Systems](https://www.pega.com/contact-us). Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

6. O aplicativo de sistemas pega Systems precisa que as asserções SAML estejam em um formato específico. Para obtê-los no formato correto, você precisa adicionar mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra os atributos padrão. Selecione o ícone **Editar** para abrir a caixa de diálogo **atributos de usuário** :

    ![Atributos de usuário](common/edit-attribute.png)

7. Além dos atributos mostrados na captura de tela anterior, o aplicativo de sistemas pega Systems requer mais alguns atributos a serem passados de volta na resposta SAML. Na seção **declarações do usuário** da caixa de diálogo **atributos de usuário** , conclua as seguintes etapas para adicionar esses atributos de token SAML:

    
   - `uid`
   - `cn`
   - `mail`
   - `accessgroup`  
   - `organization`  
   - `orgdivision`
   - `orgunit`
   - `workgroup`  
   - `Phone`

    > [!NOTE]
    > Esses valores são específicos para sua organização. Forneça os valores apropriados.

    1. Selecione **Adicionar nova declaração** para abrir a caixa de diálogo **gerenciar declarações do usuário** :

    ![Selecione Adicionar nova declaração](common/new-save-attribute.png)

    ![Caixa de diálogo Gerenciar declarações do usuário](common/new-attribute-details.png)

    1. Na caixa **nome** , digite o nome do atributo mostrado para essa linha.

    1. Deixe a caixa **namespace** vazia.

    1. Para a **origem**, selecione **atributo**.

    1. Na lista **atributo de origem** , selecione o valor do atributo mostrado para essa linha.

    1. Selecione **Ok**.

    1. Selecione **Guardar**.

8. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , selecione o link **baixar** ao lado de XML de **metadados de Federação**, de acordo com seus requisitos e salve o certificado em seu computador:

    ![Link de download de certificado](common/metadataxml.png)

9. Na seção **Configurar sistemas pega Systems** , copie as URLs apropriadas com base em seus requisitos.

    ![Copiar as URLs de configuração](common/copy-configuration-urls.png)

    1. **URL de logon**.

    1. **Identificador do Azure ad**.

    1. **URL de logout**.

### <a name="configure-pega-systems-single-sign-on"></a>Configurar o logon único do pega Systems Systems

1. Para configurar o logon único no lado do **pega Systems Systems** , entre no portal do pega Systems com uma conta de administrador em outra janela do navegador.

2. Selecione **criar** > **sysadmin** > **serviço de autenticação**:

    ![Selecionar serviço de autenticação](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
3. Conclua as etapas a seguir na tela **Criar serviço de autenticação** .

    ![Tela Criar serviço de autenticação](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    1. Na lista **tipo** , selecione **SAML 2,0**.

    1. Na caixa **nome** , digite qualquer nome (por exemplo, **SSO do Azure ad**).

    1. Na caixa **Descrição curta** , insira uma descrição.  

    1. Selecione **criar e abrir**.
    
4. Na seção **informações do IDP (provedor de identidade)** , selecione **importar metadados IDP** e navegue até o arquivo de metadados que você baixou do portal do Azure. Clique em **Enviar** para carregar os metadados:

    ![Seção informações do IdP (provedor de identidade)](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
    A importação preencherá os dados IdP, conforme mostrado aqui:

    ![Dados IdP importados](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
6. Conclua as etapas a seguir na seção **configurações do provedor de serviços (SP)** .

    ![Configurações do provedor de serviços](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    1. Copie o valor de **identificação da entidade** e cole-o na caixa **identificador** na seção **configuração básica do SAML** no portal do Azure.

    1. Copie o valor do **local do serviço de consumidor de asserção (ACS)** e cole-o na caixa **URL de resposta** na seção **configuração básica do SAML** no portal do Azure.

    1. Selecione **desabilitar assinatura de solicitação**.

7. Selecione **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta seção, você criará um usuário de teste chamado Brenda Simon no portal do Azure.

1. Na portal do Azure, selecione **Azure Active Directory** no painel esquerdo, selecione **usuários**e, em seguida, selecione **todos os usuários**:

    ![Selecionar todos os usuários](common/users.png)

2. Selecione **novo usuário** na parte superior da tela:

    ![Selecionar novo usuário](common/new-user.png)

3. Na caixa de diálogo **usuário** , conclua as etapas a seguir.

    ![Caixa de diálogo usuário](common/user-properties.png)

    a. Na caixa **nome** , digite **brendafernandes**.
  
    b. Na caixa **nome de usuário** , digite **brendafernandes @ \<yourcompanydomain >. \<extension >** . (Por exemplo, BrittaSimon@contoso.com.)

    c. Selecione **Mostrar senha**e, em seguida, anote o valor que está na caixa **senha** .

    d. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure, concedendo a ela acesso ao pega Systems Systems.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **sistemas pega Systems**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **pega Systems Systems**.

    ![Lista de aplicativos](common/all-applications.png)

3. No painel esquerdo, selecione **usuários e grupos**:

    ![Selecionar usuários e grupos](common/users-groups-blade.png)

4. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![Selecione Adicionar usuário](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você espera um valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista. Clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , selecione **atribuir**.

### <a name="create-a-pega-systems-test-user"></a>Criar um usuário de teste do pega Systems Systems

Em seguida, você precisa criar um usuário chamado Brenda Simon em pega Systems Systems. Trabalhe com a [equipe de suporte do pega Systems Systems](https://www.pega.com/contact-us) para criar usuários.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Agora você precisa testar sua configuração de logon único do Azure AD usando o painel de acesso.

Ao selecionar o bloco pega Systems Systems no painel de acesso, você deverá ser conectado automaticamente à instância do pega Systems Systems para a qual você configurou o SSO. Para obter mais informações, consulte [acessar e usar aplicativos no portal meus aplicativos](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais para integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)