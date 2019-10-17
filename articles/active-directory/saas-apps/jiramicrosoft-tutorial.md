---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o SSO do SAML para o JIRA da Microsoft | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SSO do SAML para o JIRA da Microsoft.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4b663047-7f88-443b-97bd-54224b232815
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20d5db9656dbcab31d73e504f1ca67fd577aec8e
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72439752"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jira-saml-sso-by-microsoft"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o SSO do SAML para o JIRA da Microsoft

Neste tutorial, você aprenderá a integrar o SSO do SAML para o JIRA da Microsoft com o Azure Active Directory (Azure AD). Ao integrar o SSO do SAML para o JIRA da Microsoft ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao SSO do SAML para o JIRA da Microsoft.
* Habilite seus usuários a serem conectados automaticamente ao SSO do SAML para o JIRA da Microsoft com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="description"></a>Descrição

Use sua conta de Microsoft Azure Active Directory com o servidor Atlassian JIRA para habilitar o logon único. Dessa forma, todos os usuários da sua organização podem usar as credenciais do Azure AD para entrar no aplicativo JIRA. Este plug-in usa SAML 2,0 para Federação.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao SSO do SAML para o JIRA da Microsoft, você precisa dos seguintes itens:

- Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
- JIRA Core e software 6,4 para 8,0 ou JIRA Service Desk 3,0 para 3,5 devem ser instalados e configurados no Windows 64-versão de bits
- O servidor JIRA está habilitado para HTTPS
- Observe que as versões com suporte para o plug-in JIRA são mencionadas na seção abaixo.
- O servidor JIRA é acessível na Internet especialmente para a página de logon do Azure AD para autenticação e deve ser capaz de receber o token do Azure AD
- As credenciais de administrador são configuradas em JIRA
- O websudo está desabilitado no JIRA
- Usuário de teste criado no aplicativo do servidor JIRA

> [!NOTE]
> Para testar as etapas neste tutorial, não recomendamos o uso de um ambiente de produção de JIRA. Teste a integração primeiro no ambiente de desenvolvimento ou preparo do aplicativo e, em seguida, use o ambiente de produção.

Para começar, você precisa dos seguintes itens:

* Não use seu ambiente de produção, a menos que seja necessário.
* SSO do SAML do JIRA pela assinatura do Microsoft SSO (logon único) habilitado.

## <a name="supported-versions-of-jira"></a>Versões com suporte do JIRA

* JIRA Core e software: 6,4 para 8.2.4
* JIRA Service Desk 3.0.0 to 4.2.1
* O JIRA também dá suporte a 5,2. Para obter mais detalhes, clique em [Microsoft Azure Active Directory logon único para JIRA 5,2](jira52microsoft-tutorial.md)

> [!NOTE]
> Observe que nosso plug-in JIRA também funciona no Ubuntu versão 16, 4 e Linux.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* SSO do SAML do JIRA da Microsoft dá suporte ao SSO iniciado pelo **SP**

## <a name="adding-jira-saml-sso-by-microsoft-from-the-gallery"></a>Adicionando o SSO do SAML do JIRA da Microsoft por meio da Galeria

Para configurar a integração do SSO do SAML para o JIRA da Microsoft ao Azure AD, você precisa adicionar o SSO do SAML do JIRA da Microsoft à sua lista de aplicativos SaaS gerenciados por meio da galeria.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite SSO do **SAML do JIRA da Microsoft** na caixa de pesquisa.
1. Selecione **SSO do SAML do JIRA da Microsoft** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-jira-saml-sso-by-microsoft"></a>Configurar e testar o logon único do Azure AD para SSO do SAML para o JIRA da Microsoft

Configure e teste o SSO do Azure AD com o SSO do SAML para o JIRA da Microsoft usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no SSO do SAML para o JIRA da Microsoft.

Para configurar e testar o SSO do Azure AD com o SSO do SAML para o JIRA da Microsoft, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do SAML do JIRA pelo Microsoft SSO](#configure-jira-saml-sso-by-microsoft-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar JIRA SAML SSO da Microsoft Test User](#create-jira-saml-sso-by-microsoft-test-user)** – para ter um equivalente de B. Simon no SSO do SAML do JIRA da Microsoft que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **SSO do SAML do JIRA pela Microsoft** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<domain:port>/plugins/servlet/saml/auth`

    b. Na caixa **identificador** , digite uma URL usando o seguinte padrão: `https://<domain:port>/`

    c. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador, a URL de resposta e a URL de logon reais. A porta é opcional, caso seja uma URL nomeada. Esses valores são recebidos durante a configuração do plug-in Jira, que é explicada posteriormente no tutorial.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique no botão Copiar para copiar a URL de **metadados de Federação do aplicativo** e salvá-la no computador.

    ![O link de download do certificado](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B. Simon.

1. No painel esquerdo na portal do Azure, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.
1. Selecione **novo usuário** na parte superior da tela.
1. Nas propriedades do **usuário** , siga estas etapas:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome de usuário** , insira o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **senha** .
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao SSO do SAML para o JIRA da Microsoft.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **SSO do SAML para o JIRA da Microsoft**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![O link "usuários e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-jira-saml-sso-by-microsoft-sso"></a>Configurar o SSO do SAML para o JIRA da Microsoft

1. Em uma janela diferente do navegador da Web, entre em sua instância do JIRA como administrador.

2. Focalize engrenagem e clique nos **Complementos**.

    ![Configurar logon único](./media/jiramicrosoft-tutorial/addon1.png)

3. Baixe o plug-in do [centro de download da Microsoft](https://www.microsoft.com/download/details.aspx?id=56506). Carregue manualmente o plug-in fornecido pela Microsoft usando o menu **carregar complemento** . O download do plug-in é abordado no [contrato de serviço da Microsoft](https://www.microsoft.com/servicesagreement/).

    ![Configurar logon único](./media/jiramicrosoft-tutorial/addon12.png)

4. Para executar o cenário de proxy reverso JIRA ou o cenário de balanceador de carga, execute as seguintes etapas:

    > [!NOTE]
    > Você deve configurar o servidor primeiro com as instruções abaixo e, em seguida, instalar o plug-in.

    a. Adicione o atributo abaixo na porta do **conector** no arquivo **Server. xml** do aplicativo JIRA Server.

    `scheme="https" proxyName="<subdomain.domain.com>" proxyPort="<proxy_port>" secure="true"`

    ![Configurar logon único](./media/jiramicrosoft-tutorial/reverseproxy1.png)

    b. Altere a **URL base** nas **configurações do sistema** de acordo com o proxy/balanceador de carga.

    ![Configurar logon único](./media/jiramicrosoft-tutorial/reverseproxy2.png)

5. Depois que o plug-in for instalado, ele aparecerá na seção Complementos **instalados pelo usuário** da seção **gerenciar complemento** . Clique em **Configurar** para configurar o novo plug-in.

    ![Configurar logon único](./media/jiramicrosoft-tutorial/addon14.png)

6. Execute as seguintes etapas na página de configuração:

    ![Configurar logon único](./media/jiramicrosoft-tutorial/addon54.png)

    > [!TIP]
    > Verifique se há apenas um certificado mapeado no aplicativo para que não haja nenhum erro na resolução dos metadados. Se houver vários certificados, ao resolver os metadados, o administrador receberá um erro.

    1. Na caixa de texto **URL de metadados** , Cole o valor da URL de metadados de **Federação do aplicativo** que você copiou do portal do Azure e clique no botão **resolver** . Ele lê a URL de metadados do IdP e popula todas as informações de campos.

    1. Copie o **identificador, a URL de resposta e** os valores de URL de logon e cole-os nas caixas de caixa de **entrada identificador, URL de resposta e URL de logon** , respectivamente na seção **domínio e URLs do SSO do SAML do JIRA da Microsoft** em portal do Azure.

    1. Em **nome do botão de logon** , digite o nome do botão que sua organização deseja que os usuários vejam na tela de logon.
    
    1. Na **Descrição do botão de logon** , digite a descrição do botão que sua organização deseja que os usuários vejam na tela de logon.

    1. Em **locais de ID de usuário SAML** , selecione a ID de usuário **está no elemento nameidentifier da instrução Subject ou a** **ID de usuário está em um elemento Attribute**.  Essa ID deve ser a ID de usuário JIRA. Se a ID de usuário não for correspondida, o sistema não permitirá que os usuários entrem.

       > [!Note]
       > O local da ID de usuário padrão do SAML é o identificador de nome. Você pode alterar isso para uma opção de atributo e inserir o nome de atributo apropriado.

    1. Se você selecionar **ID de usuário estiver em uma** opção de elemento de atributo, na caixa de texto **nome do atributo** , digite o nome do atributo em que a ID de usuário é esperada.

    1. Se você estiver usando o domínio federado (como o ADFS etc.) com o Azure AD, clique na opção **habilitar descoberta de realm inicial** e configure o **nome de domínio**.

    1. Em **nome de domínio** , digite o nome de domínio aqui no caso do logon baseado no ADFS.

    1. Marque **habilitar saída única** se desejar sair do Azure ad quando um usuário sair do JIRA.
    
    1. Habilite a caixa de seleção **forçar logon do Azure** , se você quiser entrar apenas por meio de credenciais do Azure AD.
    
       > [!Note]
       > Para habilitar o formulário de logon padrão para logon de administrador na página de logon quando forçar logon do Azure estiver habilitado, adicione o parâmetro de consulta na URL do navegador.
       > `https://<domain:port>/login.jsp?force_azure_login=false`

    1. Clique no botão **salvar** para salvar as configurações.

       > [!NOTE]
       > Para obter mais informações sobre instalação e solução de problemas, visite o [Guia de administração do conector de SSO do MS JIRA](../ms-confluence-jira-plugin-adminguide.md). Também há uma [FAQ](../ms-confluence-jira-plugin-faq.md) para sua assistência.

### <a name="create-jira-saml-sso-by-microsoft-test-user"></a>Criar o SSO do SAML do JIRA pelo usuário de teste da Microsoft

Para permitir que os usuários do Azure AD entrem no servidor local do JIRA, eles devem ser provisionados no SSO do SAML para o JIRA da Microsoft. Para o SSO do SAML para o JIRA da Microsoft, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no servidor local do JIRA como administrador.

2. Passe o mouse em engrenagem e clique no **Gerenciamento de usuários**.

    ![Adicionar funcionário](./media/jiramicrosoft-tutorial/user1.png)

3. Você será redirecionado para a página de acesso de administrador para inserir a **senha** e clicar no botão **confirmar** .

    ![Adicionar funcionário](./media/jiramicrosoft-tutorial/user2.png)

4. Na seção da guia **Gerenciamento de usuário** , clique em **criar usuário**.

    ![Adicionar funcionário](./media/jiramicrosoft-tutorial/user3.png) 

5. Na página da caixa de diálogo **"criar novo usuário"** , execute as seguintes etapas:

    ![Adicionar funcionário](./media/jiramicrosoft-tutorial/user4.png) 

    a. Na caixa de texto **endereço de email** , digite o endereço de email do usuário, como B.simon@contoso.com.

    b. Na caixa de texto **nome completo** , digite o nome completo do usuário, como B. Simon.

    c. Na caixa de texto **username** , digite o email do usuário, como B.simon@contoso.com.

    d. Na caixa de texto **senha** , digite a senha do usuário.

    e. Clique em **criar usuário**.

## <a name="test-sso"></a>Testar SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco SSO do SAML do JIRA pela Microsoft no painel de acesso, você deverá ser conectado automaticamente ao SSO do SAML do JIRA da Microsoft para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o SSO do SAML para o JIRA da Microsoft com o Azure AD](https://aad.portal.azure.com/)
