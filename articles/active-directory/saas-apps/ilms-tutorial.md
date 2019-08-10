---
title: 'Tutorial: Integração do Azure Active Directory com o iLMS | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o iLMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: d6e11639-6cea-48c9-b008-246cf686e726
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50097aec1c4a003d3494029e8f25bb13b564f207
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68944029"
---
# <a name="tutorial-integrate-ilms-with-azure-active-directory"></a>Tutorial: Integrar o iLMS ao Azure Active Directory

Neste tutorial, você aprenderá a integrar o iLMS com o Azure Active Directory (Azure AD). Ao integrar o iLMS ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao iLMS.
* Habilite seus usuários a serem conectados automaticamente ao iLMS com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* assinatura habilitada para SSO (logon único) do iLMS.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. o iLMS dá suporte ao **SP e** ao SSO iniciado pelo IDP

## <a name="adding-ilms-from-the-gallery"></a>Adicionando o iLMS da Galeria

Para configurar a integração do iLMS ao Azure AD, você precisará adicionar o iLMS da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **iLMS** na caixa de pesquisa.
1. Selecione **iLMS** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configure e teste o SSO do Azure AD com o iLMS usando um usuário de teste chamado **Brenda Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no iLMS.

Para configurar e testar o SSO do Azure AD com o iLMS, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
2. **[Configurar o SSO do iLMS](#configure-ilms-sso)** – para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar usuário de teste do iLMS](#create-ilms-test-user)** – para ter um equivalente de Brenda Simon no iLMS que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **iLMS** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na página **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , insira os valores para os seguintes campos:

    a. Na caixa de texto **identificador** , Cole o valor do **identificador** que você copia da seção **provedor de serviços** de configurações do SAML no portal de administração do iLMS.

    b. Na caixa de texto **URL de resposta** , Cole o valor do **ponto de extremidade (URL)** que você copia da seção **provedor de serviços** das configurações do SAML no portal de administração do iLMS com o seguinte padrão`https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

1. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    Na caixa de texto **URL de logon** , Cole o valor do **ponto de extremidade (URL)** que você copia da seção **provedor de serviços** das configurações do SAML no portal de administração do iLMS como`https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

1. Para habilitar o provisionamento JIT, seu aplicativo iLMS espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique em **Editar** ícone para abrir a caixa de diálogo atributos de usuário.

    > [!NOTE]
    > Você precisa habilitar a **criação** de uma conta de usuário não reconhecida no iLMS para mapear esses atributos. Siga as instruções [aqui](https://support.inspiredelearning.com/help/adding-updating-and-managing-users#just-in-time-provisioning-with-saml-single-signon) para ter uma ideia sobre a configuração de atributos.

1. Além de acima, o aplicativo iLMS espera que mais alguns atributos sejam passados de volta na resposta SAML. Na seção **declarações do usuário** , na caixa de diálogo **atributos de usuário** , execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo:

    | Nome | Atributo de origem|
    | --------|------------- |
    | Divisão | user.department |
    | Região | user.state |
    | Departamento | user.jobtitle |

    a. Clique em **Adicionar nova declaração** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    b. Na caixa de texto **nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **namespace** em branco.

    d. Selecione origem como **atributo**.

    e. Na lista **atributo de origem** , digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o XML de **metadados de Federação** das opções determinadas de acordo com seu requisito e salvá-lo em seu computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Na seção **Configurar iLMS** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-ilms-sso"></a>Configurar o SSO do iLMS

1. Em uma janela diferente do navegador da Web, entre no **portal de administração do iLMS** como administrador.

2. Clique em **SSO: SAML** na guia **configurações** para abrir as configurações de SAML e execute as seguintes etapas:

    ![Configurar o início de sessão único](./media/ilms-tutorial/1.png)

3. Expanda a seção **provedor de serviços** e copie o **identificador** e o valor do **ponto de extremidade (URL)** .

    ![Configurar o início de sessão único](./media/ilms-tutorial/2.png) 

4. Na seção **provedor de identidade** , clique em **importar metadados**.

5. Selecione o arquivo de **metadados de Federação** baixado do portal do Azure na seção **certificado de autenticação SAML** .

    ![Configurar o início de sessão único](./media/ilms-tutorial/tutorial_ilms_ssoconfig1.png)

6. Se você quiser habilitar o provisionamento JIT para criar contas do iLMS para os usuários não reconhecidos, siga as etapas abaixo:

    a. Marque **criar conta de usuário não reconhecida**.

    ![Configurar o início de sessão único](./media/ilms-tutorial/tutorial_ilms_ssoconfig2.png)

    b. Mapeie os atributos no Azure AD com os atributos em iLMS. Na coluna atributo, especifique o nome dos atributos ou o valor padrão.

    c. Vá para a guia **regras de negócio** e execute as seguintes etapas:

    ![Configurar o início de sessão único](./media/ilms-tutorial/5.png)

    d. Marque **criar regiões, divisões e departamentos** não reconhecidos para criar regiões, divisões e departamentos que ainda não existem no momento do logon único.

    e. Marque **Atualizar perfil do usuário durante a entrada** para especificar se o perfil do usuário é atualizado com cada logon único.

    f. Se a opção **atualizar valores em branco para campos não obrigatórios no perfil de usuário** estiver marcada, os campos de perfil opcionais que estiverem em branco na entrada também farão com que o perfil iLMS do usuário contenha valores em branco para esses campos.

    g. Marque **Enviar email de notificação de erro** e insira o email do usuário em que você deseja receber o email de notificação de erro.

7. Clique no botão **salvar** para salvar as configurações.

    ![Configurar o início de sessão único](./media/ilms-tutorial/save.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado Brenda Simon.

1. No painel esquerdo na portal do Azure, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.
1. Selecione **novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **usuário** , siga estas etapas:
   1. No campo **Nome**, introduza `Britta Simon`.  
   1. No campo **nome de usuário** , insira o username@companydomain.extension. Por exemplo, `BrittaSimon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **senha** .
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao iLMS.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **iLMS**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-ilms-test-user"></a>Criar usuário de teste do iLMS

O aplicativo dá suporte ao provisionamento de usuário just-in-time e, após a autenticação, os usuários são criados automaticamente no aplicativo. O JIT funcionará se você tiver clicado na caixa de seleção **criar conta de usuário não reconhecida** durante a definição de configuração do SAML no portal de administração do iLMS.

Se você precisar criar um usuário manualmente, siga as etapas abaixo:

1. Entre no site da empresa do iLMS como um administrador.

2. Clique em **registrar usuário** na guia **usuários** para abrir a página **registrar usuário** .

   ![Adicionar funcionário](./media/ilms-tutorial/3.png)

3. Na página **registrar usuário** , execute as etapas a seguir.

    ![Adicionar funcionário](./media/ilms-tutorial/create_testuser_add.png)

    a. Na caixa de texto **nome** , digite o nome como Brenda.

    b. Na caixa de texto **sobrenome** , digite o sobrenome, como Simon.

    c. Na caixa de texto **ID de email** , digite o endereço de email do BrittaSimon@contoso.comusuário como.

    d. Na lista suspensa **região** , selecione o valor para região.

    e. No menu suspenso **divisão** , selecione o valor de divisão.

    f. Na lista suspensa **Departamento** , selecione o valor para departamento.

    g. Clique em **Guardar**.

    > [!NOTE]
    > Você pode enviar o email de registro para o usuário selecionando a caixa de seleção **Enviar email de registro** .

### <a name="test-sso"></a>Testar SSO

Ao selecionar o bloco iLMS no painel de acesso, você deverá entrar automaticamente no iLMS para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
