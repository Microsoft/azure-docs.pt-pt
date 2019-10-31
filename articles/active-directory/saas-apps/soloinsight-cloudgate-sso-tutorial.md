---
title: 'Tutorial: integração do Azure Active Directory com o SSO do Soloinsight-CloudGate | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SSO Soloinsight-CloudGate.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 9263c241-85a4-4724-afac-0351d6275958
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b88822f164e0fe8cdf55eddfa981644f725e01f3
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73159926"
---
# <a name="tutorial-integrate-soloinsight-cloudgate-sso-with-azure-active-directory"></a>Tutorial: integrar o SSO do Soloinsight-CloudGate com o Azure Active Directory

Neste tutorial, você aprenderá a integrar o SSO do Soloinsight-CloudGate com o Azure Active Directory (Azure AD). Ao integrar o SSO do Soloinsight-CloudGate ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao SSO do Soloinsight-CloudGate.
* Habilite seus usuários a entrar automaticamente no SSO do Soloinsight-CloudGate com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* Assinatura habilitada para SSO (logon único) do Soloinsight-CloudGate SSO.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. O SSO do Soloinsight-CloudGate dá suporte ao SSO iniciado pelo **SP** .

## <a name="adding-soloinsight-cloudgate-sso-from-the-gallery"></a>Adicionando o SSO do Soloinsight-CloudGate da Galeria

Para configurar a integração do SSO do Soloinsight-CloudGate ao Azure AD, você precisará adicionar o Soloinsight-CloudGate SSO da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **SSO Soloinsight-CloudGate** na caixa de pesquisa.
1. Selecione **Soloinsight-CLOUDGATE SSO** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Configure e teste o SSO do Azure AD com o SSO Soloinsight-CloudGate usando um usuário de teste chamado **Brenda Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no SSO do Soloinsight-CloudGate.

Para configurar e testar o SSO do Azure AD com o SSO do Soloinsight-CloudGate, conclua os seguintes blocos de construção:

1. **[Configure o SSO do Azure ad](#configure-azure-ad-sso)** para permitir que seus usuários usem esse recurso.
2. **[Configure o SSO do Soloinsight-CloudGate](#configure-soloinsight-cloudgate-sso)** para definir as configurações de SSO no lado do aplicativo.
3. **[Crie um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribua o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** para permitir que o Brenda Simon use o logon único do Azure AD.
5. **[Crie um usuário de teste do SSO do Soloinsight-CloudGate](#create-soloinsight-cloudgate-sso-test-user)** para ter um equivalente de Brenda Simon no SSO Soloinsight-CloudGate que esteja vinculado à representação do usuário no Azure AD.
6. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **SSO do Soloinsight-CloudGate** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na página **configuração básica do SAML** , insira os valores para os seguintes campos:

    1. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.sigateway.com/login`

    1. Na caixa de texto **identificador (ID da entidade)** , digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.sigateway.com/process/sso`

   > [!NOTE]
   > Esses valores não são reais. Atualize esses valores com a URL de entrada e o identificador reais que são explicados posteriormente na seção **Configurar logon único do SSO do Soloinsight-CloudGate** do tutorial.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

   ![O link de download do certificado](common/certificatebase64.png)

1. Na seção **Configurar o SSO do Soloinsight-CloudGate** , copie as URLs apropriadas com base em seu requisito.

   ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-soloinsight-cloudgate-sso"></a>Configurar o SSO do Soloinsight-CloudGate

1. Para automatizar a configuração no SSO do Soloinsight-CloudGate, você precisa instalar a **extensão do navegador de entrada seguro de meus aplicativos** clicando em **instalar a extensão**.

    ![Extensão de meus aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, clique em **Configurar o Soloinsight-CLOUDGATE SSO** o direcionará para o aplicativo SSO Soloinsight-CloudGate. A partir daí, forneça as credenciais de administrador para entrar no SSO do Soloinsight-CloudGate. A extensão do navegador irá configurar automaticamente o aplicativo para você e automatizar as etapas de 3-8.

    ![Configuração da instalação](common/setup-sso.png)

3. Se você quiser configurar o SSO do Soloinsight-CloudGate manualmente, abra uma nova janela do navegador da Web e entre no site da empresa de SSO do Soloinsight-CloudGate como administrador e execute as seguintes etapas:

4. Para obter os valores que devem ser colados no portal do Azure ao configurar o SAML básico, entre no portal da Web do CloudGate usando suas credenciais e, em seguida, acesse as configurações de SSO, que podem ser encontradas nos seguintes caminhos **página inicial > administração > configurações do sistema > Geral**.

    ![Configurações de SSO do CloudGate](./media/soloinsight-cloudgate-sso-tutorial/sso-main-settings.png)

5. **URL do consumidor SAML**

    * Copie os links disponíveis nos campos **URL do consumidor SAML** e **URL de redirecionamento** e cole-os na seção de **configuração portal do Azure básica do SAML** para os campos **identificador (ID da entidade)** e **URL de resposta** , respectivamente.

        ![SAMLIdentifier](./media/soloinsight-cloudgate-sso-tutorial/saml-identifier.png)

6. **Certificado de autenticação SAML**

    * Vá para a origem do arquivo de certificado (Base64) que foi baixado de portal do Azure listas de certificados de autenticação SAML e clique com o botão direito do mouse nele. Escolha **Editar com a opção bloco de notas + +** na lista. 

        ![SAMLcertificate](./media/soloinsight-cloudgate-sso-tutorial/certificate-file.png)

    * Copie o conteúdo no arquivo do bloco de notas do certificado (Base64) + +.

        ![Cópia do certificado](./media/soloinsight-cloudgate-sso-tutorial/certificate-copy.png)

    * Cole o conteúdo no campo de **certificado** configurações de SSO do portal da Web do CloudGate e clique no botão salvar.

        ![Portal de certificados](./media/soloinsight-cloudgate-sso-tutorial/certificate-portal.png)

7. **Grupo padrão**

    * Selecione **administrador de negócios** na lista suspensa da opção **grupo padrão** no portal da Web do CloudGate

        ![Grupo padrão](./media/soloinsight-cloudgate-sso-tutorial/default-group.png)

8. **Identificador do AD e URL de logon**

    * A **URL de logon** copiada do portal do Azure configurar as configurações de **SSO Soloinsight-CloudGate** devem ser inseridas na seção Configurações de SSO do portal da Web do CloudGate.

    * Cole o link **URL de logon** de portal do Azure no campo URL de **logon do AD** do portal da Web do CloudGate.

    * Cole o link do **identificador do Azure ad** de portal do Azure no campo **identificador de AD** do portal da Web do CloudGate

        ![Logon do AD](./media/soloinsight-cloudgate-sso-tutorial/ad-login.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado Brenda Simon.

1. No painel esquerdo na portal do Azure, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.
1. Selecione **novo usuário** na parte superior da tela.
1. Nas propriedades do **usuário** , siga estas etapas:
   1. No campo **Nome**, introduza `Britta Simon`.  
   1. No campo **nome de usuário** , insira o username@companydomain.extension. Por exemplo, `BrittaSimon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **senha** .
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao Soloinsight-CloudGate SSO.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Soloinsight-CLOUDGATE SSO**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![O link "usuários e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-soloinsight-cloudgate-sso-test-user"></a>Criar usuário de teste do SSO do Soloinsight-CloudGate

Para criar um usuário de teste, selecione **funcionários** no menu principal do portal da Web do CloudGate e preencha o formulário Adicionar novo funcionário. O nível de autoridade que deve ser atribuído ao usuário de teste é **administrador de negócios** clique em **criar** uma vez que todos os campos obrigatórios são preenchidos.

![Teste de funcionário](./media/soloinsight-cloudgate-sso-tutorial/employee-test.png)

### <a name="test-sso"></a>Testar SSO

Ao selecionar o bloco de SSO do Soloinsight-CloudGate no painel de acesso, você deverá ser conectado automaticamente ao SSO do Soloinsight-CloudGate para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)