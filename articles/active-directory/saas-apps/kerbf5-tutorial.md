---
title: 'Tutorial: Azure Active Directory integração de SSO (logon único) com F5 | Microsoft Docs'
description: Saiba como configurar o logon único entre Azure Active Directory e F5.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 39382eab-05fe-4dc2-8792-62d742dfb4e1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea331bbabe238c351921a02a5012a9f8a087646f
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70166311"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Tutorial: Azure Active Directory integração de SSO (logon único) com F5

Neste tutorial, você aprenderá a integrar o F5 ao Azure Active Directory (Azure AD). Ao integrar o F5 ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao F5.
* Habilite seus usuários a serem automaticamente conectados ao F5 com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* F5 assinatura habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O F5 dá suporte a **SP** iniciado pelo
* O SSO do F5 pode ser configurado de três maneiras diferentes.

- [Configurar o logon único F5 para o aplicativo Kerberos](#configure-f5-single-sign-on-for-kerberos-application)

- [Configurar o logon único F5 para o aplicativo baseado em cabeçalho](headerf5-tutorial.md)

- [Configurar o logon único F5 para o aplicativo Kerberos avançado](advance-kerbf5-tutorial.md)

## <a name="adding-f5-from-the-gallery"></a>Adicionando F5 da Galeria

Para configurar a integração do F5 ao Azure AD, você precisa adicionar o F5 da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **F5** na caixa de pesquisa.
1. Selecione **F5** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>Configurar e testar o logon único do Azure AD para o F5

Configure e teste o SSO do Azure AD com F5 usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado em F5.

Para configurar e testar o SSO do Azure AD com o F5, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do F5](#configure-f5-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Create F5 Test User](#create-f5-test-user)** – para ter um equivalente de B. Simon em F5 que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos **F5** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , insira os valores para os seguintes campos:

    a. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão:`https://<YourCustomFQDN>.f5.com/`

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão:`https://<YourCustomFQDN>.f5.com/`

1. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão:`https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador, a URL de resposta e a URL de logon reais. Contate a [equipe de suporte ao cliente do F5](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Na seção **Configurar F5** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao F5.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **F5**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-f5-sso"></a>Configurar o SSO do F5

- [Configurar o logon único F5 para o aplicativo baseado em cabeçalho](headerf5-tutorial.md)

- [Configurar o logon único F5 para o aplicativo Kerberos avançado](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-kerberos-application"></a>Configurar o logon único F5 para o aplicativo Kerberos

1. Abra uma nova janela do navegador da Web e entre no site da sua empresa do F5 (Kerberos) como administrador e execute as seguintes etapas:

1. Você precisa importar o certificado de metadados para o F5 (Kerberos), que será usado posteriormente no processo de instalação. Vá para **sistema > gerenciamento de certificados > tráfego gerenciamento de certificado > > lista de certificados SSL**. Clique em **importar** do canto direito.

    ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure01.png) 

1. Além disso, você também precisa de um **certificado SSL** para`Kerbapp.superdemo.live`o nome do host (), neste exemplo, usamos o certificado curinga.

    ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure02.png) 
 
1. Vá para – **F5 Big-IP clique em acessar > configuração guiada > federação > provedor de serviços SAML**.

    ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure03.png) 

    ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure04.png)

1. Especifique a **ID da entidade** (o mesmo que você configurou na configuração de aplicativo do Azure AD)

    ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure05.png) 

1. Crie um novo servidor virtual, especifique o **endereço de destino**. Escolha o **certificado curinga** (ou o certificado que você carregou para o aplicativo) que carregamos anteriormente e a **chave privada associada**.

    ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure06.png)

1. Carregue os **metadados** de configuração e especifique um novo **nome para o conector IDP do SAML** e também será necessário especificar o certificado de Federação que foi carregado anteriormente.

    ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure07.png)  

1. **Criar novo** Pool de aplicativos de back-end, especifique os **endereços IP** dos servidores de aplicativos de back-end.
 
    ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure08.png)

1. Em **configurações de logon único**, escolha **Kerberos** e selecione **Configurações avançadas**. A solicitação precisa ser criada no `user@domain.suffix`.

1. Na **origem do nome** de `session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`usuário, especifique. Consulte o apêndice para obter uma lista completa de variáveis e valores.
Nome da conta é a conta de delegação F5 criada (verifique a documentação F5).

    ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure09.png)   

1. Contate a [equipe de suporte do cliente F5 (Kerberos)](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) para obter informações sobre a documentação da **verificação de ponto de extremidade** .

    ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure10.png) 

1. Contate a [equipe de suporte do cliente F5 (Kerberos)](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) para obter **as propriedades de gerenciamento de sessão** detalhes da documentação.

    ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure11.png) 
 
1. **Examine Resumo** e clique em **implantar**.
 
    ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure12.png)

    ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure13.png)

### <a name="create-f5-test-user"></a>Criar usuário de teste do F5

Nesta seção, você criará um usuário chamado B. Simon em F5. Trabalhe com a [equipe de suporte ao cliente F5](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) para adicionar os usuários à plataforma F5. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único. 

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco F5 no painel de acesso, você deverá ser conectado automaticamente ao F5 para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o F5 com o Azure AD](https://aad.portal.azure.com/)

- [Configurar o logon único F5 para o aplicativo baseado em cabeçalho](headerf5-tutorial.md)

- [Configurar o logon único F5 para o aplicativo Kerberos avançado](advance-kerbf5-tutorial.md)

