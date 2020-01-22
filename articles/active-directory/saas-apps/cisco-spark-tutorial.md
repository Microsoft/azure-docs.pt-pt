---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o Cisco WebEx | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Cisco WebEx.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f6ab2d5811060b7dc36323a80fed6961b8cf5a9
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76290687"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o Cisco WebEx

Neste tutorial, você aprenderá a integrar o Cisco WebEx ao Azure Active Directory (Azure AD). Ao integrar o Cisco WebEx ao Azure AD, você pode:

* Controle no Azure AD que tem acesso ao Cisco WebEx.
* Habilite seus usuários a serem conectados automaticamente ao Cisco WebEx com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Cisco WebEx.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Cisco WebEx dá suporte ao SSO iniciado pelo **SP** .
* O Cisco WebEx dá suporte ao provisionamento **automatizado** de usuários.
* Depois de configurar o Cisco WebEx, você pode impor controles de sessão, que protegem vazamento e pós-infiltração dos dados confidenciais de sua organização em tempo real. Os controles de sessão se estendem do acesso condicional. [Saiba como impor o controle de sessão com Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-cisco-webex-from-the-gallery"></a>Adicionando o Cisco WebEx da Galeria

Para configurar a integração do Cisco WebEx ao Azure AD, você precisa adicionar o Cisco WebEx da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **Cisco WebEx** na caixa de pesquisa.
1. Selecione **Cisco WebEx** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cisco-webex"></a>Configurar e testar o logon único do Azure AD para o Cisco WebEx

Configure e teste o SSO do Azure AD com o Cisco WebEx usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Cisco WebEx.

Para configurar e testar o SSO do Azure AD com o Cisco WebEx, conclua os seguintes blocos de construção:

1. **[Configure o SSO do Azure ad](#configure-azure-ad-sso)** para permitir que seus usuários usem esse recurso.
    1. **[Crie um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B. Simon.
    1. **[Atribua o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** para habilitar B. Simon para usar o logon único do Azure AD.
2. **[Configure o Cisco WebEx](#configure-cisco-webex)** para definir as configurações de SSO no lado do aplicativo.
    1. **[Crie um usuário de teste do Cisco WebEx](#create-cisco-webex-test-user)** para ter um equivalente de B. Simon no Cisco WebEx que esteja vinculado à representação de usuário do Azure AD.
3. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Cisco WebEx** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , carregue o arquivo de **metadados do provedor de serviços** baixado e configure o aplicativo executando as seguintes etapas:

    >[!Note]
    >Você obterá o arquivo de metadados do provedor de serviços na seção **Configurar o Cisco WebEx** , que é explicada posteriormente no tutorial. 

    a. Clique em **carregamento de ficheiro de metadados**.

    b. Clique em **logótipo da pasta** para selecionar o ficheiro de metadados e clique em **carregar**.

    c. Após a conclusão bem-sucedida do carregamento do arquivo de metadados do provedor de serviços, os valores do **identificador** e da **URL de resposta** serão preenchidos automaticamente na seção **configuração básica do SAML** :

    Na caixa de texto **URL de logon** , Cole o valor da **URL de resposta**, que é preenchido automaticamente pelo upload do arquivo de metadados do SP.

1. O aplicativo Cisco WebEx espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![imagem](common/default-attributes.png)

1. Além de acima, o aplicativo Cisco WebEx espera que mais alguns atributos sejam passados de volta na resposta SAML, que são mostrados abaixo. Esses atributos também são preenchidos previamente, mas você pode examiná-los de acordo com seus requisitos.
  
    | Nome |  Atributo de origem|
    | ---------------|--------- |
    | uid | user.userprincipalname |

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

   ![O link de download de certificado](common/metadataxml.png)

1. Na seção **Configurar o Cisco WebEx** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao Cisco WebEx.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Cisco WebEx**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-cisco-webex"></a>Configurar o Cisco WebEx

1. Para automatizar a configuração no Cisco WebEx, você precisa instalar a **extensão do navegador de entrada seguro de meus aplicativos** clicando em **instalar a extensão**.

    ![Extensão de meus aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, clique em **Configurar o Cisco WebEx** direcionará você para o aplicativo Cisco WebEx. A partir daí, forneça as credenciais de administrador para entrar no Cisco WebEx. A extensão do navegador irá configurar automaticamente o aplicativo para você e automatizar as etapas de 3-8.

    ![Configuração da instalação](common/setup-sso.png)

3. Se você quiser configurar o Cisco WebEx manualmente, entre no [Gerenciamento de colaboração de nuvem da Cisco](https://admin.ciscospark.com/) com suas credenciais de administrador completas.

4. Selecione **configurações** e, na seção **autenticação** , clique em **Modificar**.

    ![Configurar o início de sessão único](./media/cisco-spark-tutorial/tutorial-cisco-spark-10.png)
  
5. Selecione **integrar um provedor de identidade de terceiros. (Avançado)** e vá para a próxima tela.

6. Na página **importar metadados IDP** , arraste e solte o arquivo de metadados do Azure ad na página ou use a opção navegador de arquivos para localizar e carregar o arquivo de metadados do Azure AD. Em seguida, selecione **exigir certificado assinado por uma autoridade de certificação em metadados (mais seguro)** e clique em **Avançar**.

    ![Configurar o início de sessão único](./media/cisco-spark-tutorial/tutorial-cisco-spark-11.png)

7. Selecione **testar conexão de SSO**e, quando uma nova guia do navegador for aberta, autentique-se com o Azure ad entrando.

8. Retorne à guia navegador de **Gerenciamento de colaboração de nuvem da Cisco** . Se o teste tiver sido bem-sucedido, selecione **esse teste foi bem-sucedido. Habilite a opção logon único** e clique em **Avançar**.

### <a name="create-cisco-webex-test-user"></a>Criar usuário de teste do Cisco WebEx

Nesta seção, você criará um usuário chamado B. Simon no Cisco WebEx. Nesta seção, você criará um usuário chamado B. Simon no Cisco WebEx.

1. Acesse o [Gerenciamento de colaboração de nuvem da Cisco](https://admin.ciscospark.com/) com suas credenciais de administrador completas.

2. Clique em **usuários** e em **gerenciar usuários**.
   
    ![Configurar o início de sessão único](./media/cisco-spark-tutorial/tutorial-cisco-spark-12.png) 

3. Na janela **gerenciar usuário** , selecione **Adicionar ou modificar usuários manualmente** e clique em **Avançar**.

4. Selecione **nomes e endereço de email**. Em seguida, preencha a caixa de texto da seguinte maneira:

    ![Configurar o início de sessão único](./media/cisco-spark-tutorial/tutorial-cisco-spark-13.png) 

    a. Na caixa de texto **nome** , digite o nome do usuário como **B**.

    b. Na caixa de texto **sobrenome** , digite o sobrenome do usuário, como **Simon**.

    c. Na caixa de texto **endereço de email** , digite o endereço de email do usuário, como b.simon@contoso.com.

5. Clique no sinal de adição para adicionar B. Simon. Clique depois em **Seguinte**.

6. Na janela **Adicionar serviços para usuários** , clique em **salvar** e em **concluir**.

## <a name="test-sso"></a>Testar SSO

Quando você seleciona o bloco Cisco WebEx no painel de acesso, deve ser automaticamente conectado ao Cisco WebEx para o qual você configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Cisco WebEx com o Azure AD](https://aad.portal.azure.com)

- [O que é o controle de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/protect-webex)

- [Como proteger o Cisco WebEx com visibilidade e controles avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
