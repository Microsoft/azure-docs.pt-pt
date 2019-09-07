---
title: 'Tutorial: Azure Active Directory integração de SSO (logon único) com o SSO do SAML para o Confluence da Microsoft | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SSO do SAML para o Confluence da Microsoft.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1ad1cf90-52bc-4b71-ab2b-9a5a1280fb2d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1384a8c9cfc4da9e8757c26bdb3e92defdb73708
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70743693"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-confluence-saml-sso-by-microsoft"></a>Tutorial: Azure Active Directory integração de SSO (logon único) com o SSO do SAML para o Confluence da Microsoft

Neste tutorial, você aprenderá a integrar o SSO do SAML para o Confluence da Microsoft com o Azure Active Directory (Azure AD). Ao integrar o SSO do SAML para o Confluence da Microsoft ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao SSO do SAML para o Confluence da Microsoft.
* Habilite seus usuários a serem conectados automaticamente ao SSO do SAML para o Confluence da Microsoft com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="description"></a>Descrição:

Use sua conta de Microsoft Azure Active Directory com o servidor Atlassian Confluence para habilitar o logon único. Dessa forma, todos os usuários da sua organização podem usar as credenciais do Azure AD para entrar no aplicativo Confluence. Este plug-in usa SAML 2,0 para Federação.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao SSO do SAML para o Confluence da Microsoft, você precisa dos seguintes itens:

- Uma subscrição do Azure
- Aplicativo do Confluence Server instalado em um servidor de bits do Windows 64 (local ou na infraestrutura de IaaS na nuvem)
- O servidor Confluence está habilitado para HTTPS
- Observe que as versões com suporte para o plug-in Confluence são mencionadas na seção abaixo.
- O servidor Confluence é acessível na Internet especialmente para a página de logon do Azure AD para autenticação e deve ser capaz de receber o token do Azure AD
- As credenciais de administrador são configuradas em Confluence
- O websudo está desabilitado no Confluence
- Usuário de teste criado no aplicativo do servidor Confluence

> [!NOTE]
> Para testar as etapas neste tutorial, não recomendamos o uso de um ambiente de produção de Confluence. Teste a integração primeiro no ambiente de desenvolvimento ou preparo do aplicativo e, em seguida, use o ambiente de produção.

Para começar, você precisa dos seguintes itens:

* Não utilize o seu ambiente de produção, a menos que seja necessário.
* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* SSO do SAML do Confluence pela assinatura do Microsoft SSO (logon único) habilitado.

## <a name="supported-versions-of-confluence"></a>Versões com suporte do Confluence

A partir de agora, há suporte para as seguintes versões do Confluence:

- Confluence 5,0 a 5,10
- Confluence 6.0.1
- Confluence 6.1.1
- Confluence 6.2.1
- Confluence 6.3.4
- Confluence 6.4.0
- Confluence 6.5.0
- Confluence 6.6.2
- Confluence 6.7.0
- Confluence 6.8.1
- Confluence 6.9.0
- Confluence 6.10.0
- Confluence 6.11.0
- Confluence 6.12.0
- Confluence 6.15.3

> [!NOTE]
> Observe que nosso plug-in Confluence também funciona no Ubuntu versão 16, 4

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* SSO do SAML do Confluence da Microsoft dá suporte ao SSO iniciado pelo **SP**

## <a name="adding-confluence-saml-sso-by-microsoft-from-the-gallery"></a>Adicionando o SSO do SAML do Confluence da Microsoft por meio da Galeria

Para configurar a integração do SSO do SAML para o Confluence da Microsoft ao Azure AD, você precisa adicionar o SSO do SAML do Confluence da Microsoft à sua lista de aplicativos SaaS gerenciados por meio da galeria.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite SSO do **SAML do Confluence da Microsoft** na caixa de pesquisa.
1. Selecione **SSO do SAML do Confluence da Microsoft** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-confluence-saml-sso-by-microsoft"></a>Configurar e testar o logon único do Azure AD para SSO do SAML para o Confluence da Microsoft

Configure e teste o SSO do Azure AD com o SSO do SAML para o Confluence da Microsoft usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no SSO do SAML para o Confluence da Microsoft.

Para configurar e testar o SSO do Azure AD com o SSO do SAML para o Confluence da Microsoft, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do SAML do Confluence pelo Microsoft SSO](#configure-confluence-saml-sso-by-microsoft-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar CONFLUENCE SAML SSO da Microsoft Test User](#create-confluence-saml-sso-by-microsoft-test-user)** – para ter um equivalente de B. Simon no SSO do SAML do Confluence da Microsoft que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **SSO do SAML do Confluence pela Microsoft** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão:`https://<domain:port>/plugins/servlet/saml/auth`

    b. Na caixa **identificador** , digite uma URL usando o seguinte padrão:`https://<domain:port>/`

    c. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão:`https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador, a URL de resposta e a URL de logon reais. A porta é opcional, caso seja uma URL nomeada. Esses valores são recebidos durante a configuração do plug-in Confluence, que é explicada posteriormente no tutorial.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique no botão Copiar para copiar a URL de **metadados de Federação do aplicativo** e salvá-la no computador.

    ![O link de download de certificado](common/copy-metadataurl.png)

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao SSO do SAML para o Confluence da Microsoft.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **SSO do SAML para o Confluence da Microsoft**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-confluence-saml-sso-by-microsoft-sso"></a>Configurar o SSO do SAML para o Confluence da Microsoft

1. Em uma janela diferente do navegador da Web, entre em sua instância do Confluence como administrador.

1. Focalize engrenagem e clique nos **Complementos**.

    ![Configurar o início de sessão único](./media/confluencemicrosoft-tutorial/addon1.png)

1. Baixe o plug-in do [centro de download da Microsoft](https://www.microsoft.com/download/details.aspx?id=56503). Carregue manualmente o plug-in fornecido pela Microsoft usando o menu **carregar complemento** . O download do plug-in é abordado no [contrato de serviço da Microsoft](https://www.microsoft.com/servicesagreement/).

    ![Configurar o início de sessão único](./media/confluencemicrosoft-tutorial/addon12.png)

1. Para executar o cenário de proxy reverso Confluence ou o cenário de balanceador de carga, execute as seguintes etapas:

    > [!NOTE]
    > Você deve configurar o servidor primeiro com as instruções abaixo e, em seguida, instalar o plug-in.

    a. Adicione o atributo abaixo na porta do **conector** no arquivo **Server. xml** do aplicativo JIRA Server.

    `scheme="https" proxyName="<subdomain.domain.com>" proxyPort="<proxy_port>" secure="true"`

    ![Configurar o início de sessão único](./media/confluencemicrosoft-tutorial/reverseproxy1.png)

    b. Altere a **URL base** nas **configurações do sistema** de acordo com o proxy/balanceador de carga.

    ![Configurar o início de sessão único](./media/confluencemicrosoft-tutorial/reverseproxy2.png)

1. Depois que o plug-in for instalado, ele aparecerá na seção Complementos **instalados pelo usuário** da seção **gerenciar complemento** . Clique em **Configurar** para configurar o novo plug-in.

    ![Configurar o início de sessão único](./media/confluencemicrosoft-tutorial/addon13.png)

1. Execute as seguintes etapas na página de configuração:

    ![Configurar o início de sessão único](./media/confluencemicrosoft-tutorial/addon53.png)

    > [!TIP]
    > Verifique se há apenas um certificado mapeado no aplicativo para que não haja nenhum erro na resolução dos metadados. Se houver vários certificados, o administrador obterá um erro ao resolver os metadados.

    1. Na caixa de texto **URL de metadados** , Cole o valor da URL de metadados de **Federação do aplicativo** que você copiou do portal do Azure e clique no botão **resolver** . Ele lê a URL de metadados do IdP e popula todas as informações de campos.

    1. Copie o **identificador, a URL de resposta e** os valores da URL de logon e cole-os nas caixas de caixa de **entrada identificador, URL de resposta e URL de logon** , respectivamente na seção **configuração básica do SAML** , em portal do Azure.

    1. Em **nome do botão de logon** , digite o nome do botão que sua organização deseja que os usuários vejam na tela de logon.

    1. Em **locais de ID de usuário do SAML**, selecione a **ID de usuário está no elemento nameidentifier da instrução Subject ou a** **ID de usuário está em um elemento Attribute**.  Essa ID deve ser a ID de usuário Confluence. Se a ID de usuário não for correspondida, o sistema não permitirá que os usuários entrem. 

       > [!Note]
       > O local da ID de usuário padrão do SAML é o identificador de nome. Você pode alterar isso para uma opção de atributo e inserir o nome de atributo apropriado.

    1. Se você selecionar **ID de usuário estiver em uma** opção de elemento de atributo, na caixa de texto **nome do atributo** , digite o nome do atributo em que a ID de usuário é esperada. 

    1. Se você estiver usando o domínio federado (como o ADFS etc.) com o Azure AD, clique na opção **habilitar descoberta de realm inicial** e configure o **nome de domínio**.

    1. Em **nome de domínio** , digite o nome de domínio aqui no caso do logon baseado no ADFS.

    1. Marque **habilitar saída única** se desejar sair do Azure ad quando um usuário sair do Confluence. 

    1. Habilite a caixa de seleção **forçar logon do Azure** , se você quiser entrar apenas por meio de credenciais do Azure AD.

       > [!Note]
       > Para habilitar o formulário de logon padrão para logon de administrador na página de logon quando o forçar logon do Azure estiver habilitado, adicione o parâmetro de consulta na URL do navegador.
       > `https://<domain:port>/login.action?force_azure_login=false`

    1. Clique no botão **salvar** para salvar as configurações.

       > [!NOTE]
       > Para obter mais informações sobre instalação e solução de problemas, visite o [Guia de administração do conector de SSO do MS Confluence](../ms-confluence-jira-plugin-adminguide.md). Também há uma [FAQ](../ms-confluence-jira-plugin-faq.md) para sua assistência.

### <a name="create-confluence-saml-sso-by-microsoft-test-user"></a>Criar o SSO do SAML do Confluence pelo usuário de teste da Microsoft

Para permitir que os usuários do Azure AD entrem no servidor local do Confluence, eles devem ser provisionados no SSO do SAML para o Confluence da Microsoft. Para o SSO do SAML para o Confluence da Microsoft, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no servidor local do Confluence como administrador.

1. Passe o mouse em engrenagem e clique no **Gerenciamento de usuários**.

    ![Adicionar funcionário](./media/confluencemicrosoft-tutorial/user1.png)

1. Na seção usuários, clique na guia **Adicionar usuários** . Na página da caixa de diálogo **Adicionar um usuário** , execute as seguintes etapas:

    ![Adicionar funcionário](./media/confluencemicrosoft-tutorial/user2.png)

    a. Na caixa de texto **username** , digite o email do usuário, como B. Simon.

    b. Na caixa de texto **nome completo** , digite o nome completo do usuário, como B. Simon.

    c. Na caixa de texto **email** , digite o endereço de email do B.Simon@contoso.comusuário como.

    d. Na caixa de texto **senha** , digite a senha para B. Simon.

    e. Clique em **Confirmar senha** reinsira a senha.

    f. Clique em **adicionar** botão.

## <a name="test-sso"></a>Testar SSO

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco SSO do SAML do Confluence pela Microsoft no painel de acesso, você deverá ser conectado automaticamente ao SSO do SAML do Confluence da Microsoft para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o SSO do SAML para o Confluence da Microsoft com o Azure AD](https://aad.portal.azure.com/)