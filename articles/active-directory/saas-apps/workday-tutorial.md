---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o workday | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o workday.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 843dd403bc5434e2c79ee0bb85eb781b56bf3ec9
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76291452"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o workday

Neste tutorial, você aprenderá a integrar o workday ao Azure Active Directory (Azure AD). Ao integrar o workday ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao workday.
* Habilite seus usuários a entrarem automaticamente no workday com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do workday.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O workday dá suporte ao SSO iniciado pelo **SP** .

* Depois de configurar o workday, você pode impor controles de sessão, que protegem vazamento e pós-infiltração dos dados confidenciais de sua organização em tempo real. Os controles de sessão se estendem do acesso condicional. [Saiba como impor o controle de sessão com Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-workday-from-the-gallery"></a>Adicionando o workday da Galeria

Para configurar a integração do workday ao Azure AD, você precisará adicionar o workday da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **workday** na caixa de pesquisa.
1. Selecione **workday** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-workday"></a>Configurar e testar o logon único do Azure AD para o workday

Configure e teste o SSO do Azure AD com o workday usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no workday.

Para configurar e testar o SSO do Azure AD com o workday, conclua os seguintes blocos de construção:

1. **[Configure o SSO do Azure ad](#configure-azure-ad-sso)** para permitir que seus usuários usem esse recurso.
    1. **[Crie um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B. Simon.
    1. **[Atribua o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** para habilitar B. Simon para usar o logon único do Azure AD.
2. **[Configure o workday](#configure-workday)** para definir as configurações de SSO no lado do aplicativo.
    1. **[Crie o usuário de teste do workday](#create-workday-test-user)** para ter um equivalente de B. Simon no workday que esteja vinculado à representação de usuário do Azure AD.
3. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **workday** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na página **configuração básica do SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://impl.workday.com/<tenant>/login-saml2.flex`

    b. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão: `http://www.workday.com`

    c. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https://impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE]
    > Esses valores não são o real. Atualize esses valores com a URL de entrada e a URL de resposta reais. A URL de resposta deve ter um subdomínio, por exemplo: www, WD2, WD3, WD3-impl, WD5, WD5-impl).
    > O uso de algo como `http://www.myworkday.com` funciona, mas `http://myworkday.com` não. Contate a [equipe de suporte ao cliente do workday](https://www.workday.com/en-us/partners-services/services/support.html) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

6. Seu aplicativo do workday espera as asserções do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão, onde o **nameidentifier** é mapeado com **User. UserPrincipalName**. O aplicativo workday espera que **nameidentifier** seja mapeado com **User. mail**, **UPN**, etc., portanto, você precisa editar o mapeamento de atributo clicando no ícone de **edição** e alterando o mapeamento de atributo.

    ![imagem](common/edit-attribute.png)

    > [!NOTE]
    > Aqui mapeamos a ID de nome com o UPN (User. UserPrincipalName) como padrão. Você precisa mapear a ID de nome com a ID de usuário real em sua conta de workday (seu email, UPN, etc.) para o trabalho de SSO com êxito.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

   ![O link de download de certificado](common/certificatebase64.png)

1. Para modificar as opções de **assinatura** de acordo com seu requisito, clique no botão **Editar** para abrir a caixa de diálogo **certificado de autenticação SAML** .

    ![imagem](common/edit-certificate.png) 

    ![imagem](./media/workday-tutorial/signing-option.png)

    a. Selecione **assinar resposta SAML e asserção** para a **opção de assinatura**.

    b. Clicar em **Guardar**

1. Na seção **Configurar o workday** , copie as URLs apropriadas com base em seu requisito.

   ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B. Simon.

1. No painel esquerdo na portal do Azure, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.
1. Selecione **novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **usuário** , siga estas etapas:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome de usuário** , insira o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **senha** .
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao workday.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **workday**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-workday"></a>Configurar workday

1. Em uma janela diferente do navegador da Web, entre no site da empresa do workday como administrador.

2. Na **caixa de pesquisa** , pesquise com o nome **Editar configuração do locatário – segurança** no lado superior esquerdo da Home Page.

    ![Editar segurança de locatário](./media/workday-tutorial/IC782925.png "Editar segurança de locatário")

3. Na seção **URLs de redirecionamento** , execute as seguintes etapas:

    ![URLs de redirecionamento](./media/workday-tutorial/IC7829581.png "URLs de redirecionamento")

    a. Clique em **Adicionar linha**.

    b. Na caixa de texto URL de **redirecionamento de logon**, URL de **redirecionamento de tempo limite** e **URL de redirecionamento móvel** , Cole a **URL de logon** que você copiou da seção configurar o **workday** de portal do Azure.

    c. Na caixa de texto **URL de redirecionamento de logout** , Cole a **URL de logout** que você copiou da seção configurar o **workday** de portal do Azure.

    d. Em **usado para** a caixa de texto ambientes, selecione o nome do ambiente.  

   > [!NOTE]
   > O valor do atributo de ambiente é vinculado ao valor da URL do locatário:  
   > -Se o nome de domínio da URL do locatário do workday começar com impl, por exemplo: *https://www.myworkday.com/"Tenant"/login-saml2.htmld*), o atributo de **ambiente** deverá ser definido como implementação.  
   > -Se o nome de domínio começar com algo mais, você precisará entrar em contato com a [equipe de suporte do cliente do workday](https://www.workday.com/en-us/partners-services/services/support.html) para obter o valor de **ambiente** correspondente.

4. Na seção **instalação do SAML** , execute as seguintes etapas:

    ![Instalação do SAML](./media/workday-tutorial/IC782926.png "Instalação do SAML")

    a.  Selecione **habilitar autenticação SAML**.

    b.  Clique em **Adicionar linha**.

5. Na seção **provedores de identidade SAML** , execute as seguintes etapas:

    ![Provedores de identidade SAML](./media/workday-tutorial/IC7829271.png "Fornecedores de Identidade SAML")

    a. Na caixa de texto **nome do provedor de identidade** , digite um nome de provedor (por exemplo: *SPInitiatedSSO*).

    b. Na portal do Azure, na seção **Configurar o workday** , copie o valor do **identificador do Azure ad** e cole-o na caixa de texto **emissor** .

    ![Provedores de identidade SAML](./media/workday-tutorial/IC7829272.png "Fornecedores de Identidade SAML")

    c. Na portal do Azure, na seção **Configurar o workday** , copie o valor da **URL de logout** e cole-o na caixa de texto URL de resposta de **logout** .

    d. Na portal do Azure, na seção **Configurar o workday** , copie o valor da **URL de logon** e cole-o na caixa de texto URL do serviço de SSO do **IDP** .

    e. Em **usado para** a caixa de texto ambientes, selecione o nome do ambiente.

    f. Clique em **certificado de chave pública do provedor de identidade**e, em seguida, clique em **criar**.

    ![Criar](./media/workday-tutorial/IC782928.png "Create")

    g. Clique em **criar chave pública X509**.

    ![Criar](./media/workday-tutorial/IC782929.png "Create")

6. Na seção **Exibir chave pública X509** , execute as seguintes etapas:

    ![Exibir chave pública X509](./media/workday-tutorial/IC782930.png "Exibir chave pública X509")

    a. Na caixa de texto **nome** , digite um nome para o certificado (por exemplo: *PPE\_SP*).

    b. Na caixa **de texto válido de** , digite o valor de atributo válido do seu certificado.

    c.  Na caixa de texto **válido até** , digite o valor válido para o atributo do seu certificado.

    > [!NOTE]
    > Você pode obter a data de validade válida e a data válida até a partir do certificado baixado clicando duas vezes nele.  As datas são listadas na guia **detalhes** .
    >
    >

    d.  Abra seu certificado codificado em base 64 no bloco de notas e copie o conteúdo dele.

    e.  Na caixa de texto **certificado** , Cole o conteúdo da área de transferência.

    f.  Clique em **OK**.

7. Execute as seguintes etapas:

    ![Configuração de SSO](./media/workday-tutorial/WorkdaySSOConfiguratio.png "Configuração de SSO")

    a.  Na caixa de texto **ID do provedor de serviços** , digite **http://www.workday.com** .

    b. Selecione não **desinflar solicitação de autenticação iniciada pelo SP**.

    c. Como **método de assinatura de solicitação de autenticação**, selecione **SHA256**.

    ![Método de assinatura de solicitação de autenticação](./media/workday-tutorial/WorkdaySSOConfiguration.png "Método de assinatura de solicitação de autenticação")

    d. Clique em **OK**.

    ![OK](./media/workday-tutorial/IC782933.png "OK")

    > [!NOTE]
    > Verifique se você configurou o logon único corretamente. Caso você habilite o logon único com a configuração incorreta, talvez não seja possível inserir o aplicativo com suas credenciais e ser bloqueado. Nessa situação, o workday fornece uma URL de logon de backup em que os usuários podem entrar usando seu nome de usuário e senha normais no seguinte formato: [sua URL de workday]/Login.Flex? Redirect = n

### <a name="create-workday-test-user"></a>Criar usuário de teste do workday

Nesta seção, você criará um usuário chamado B. Simon no workday. Trabalhe com a [equipe de suporte ao cliente do workday](https://www.workday.com/partners-services/services/support.html) para adicionar os usuários na plataforma workday. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

## <a name="test-sso"></a>Testar SSO

Ao selecionar o bloco workday no painel de acesso, você deverá ser conectado automaticamente ao WORKDAY para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o workday com o Azure AD](https://aad.portal.azure.com)

- [O que é o controle de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/protect-workday)

- [Como proteger o workday com visibilidade e controles avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
