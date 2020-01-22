---
title: 'Tutorial: Azure Active Directory integração de SSO (logon único) com SSOGEN-Azure AD SSO gateway para Oracle E-Business Suite-EBS, PeopleSoft e JDE | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SSOGEN-Azure AD SSO gateway para Oracle E-Business Suite-EBS, PeopleSoft e JDE.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f4311031-5a4b-468e-be58-324d06220869
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74b56e30eb8851913f91cbee377204a575311375
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293679"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde"></a>Tutorial: Azure Active Directory integração de SSO (logon único) com SSOGEN-Azure AD SSO gateway para Oracle E-Business Suite-EBS, PeopleSoft e JDE

Neste tutorial, você aprenderá a integrar o SSOGEN-Azure AD SSO gateway para Oracle E-Business Suite-EBS, PeopleSoft e JDE com o Azure Active Directory (Azure AD). Ao integrar o SSOGEN-Azure AD SSO gateway para Oracle E-Business Suite-EBS, PeopleSoft e JDE com o Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao SSOGEN-Azure AD SSO gateway para Oracle E-Business Suite-EBS, PeopleSoft e JDE.
* Habilite seus usuários a entrar automaticamente no SSOGEN-Azure AD SSO gateway para Oracle E-Business Suite-EBS, PeopleSoft e JDE com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* SSOGEN-assinatura do Azure AD SSO gateway para Oracle E-Business Suite – EBS, PeopleSoft e JDE habilitada para logon único (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* SSOGEN-Azure AD SSO gateway para Oracle E-Business Suite-EBS, PeopleSoft e JDE dá suporte ao **SP e** ao SSO iniciado pelo IDP.
* Depois de configurar o SSOGEN-Azure AD SSO gateway para Oracle E-Business Suite-EBS, PeopleSoft e JDE, você pode impor controles de sessão, que protegem vazamento e pós-infiltração dos dados confidenciais de sua organização em tempo real. Os controles de sessão se estendem do acesso condicional. [Saiba como impor o controle de sessão com Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo, de modo que apenas uma instância pode ser configurada em um locatário.

## <a name="adding-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-from-the-gallery"></a>Adicionando SSOGEN-gateway de SSO do Azure AD para Oracle E-Business Suite-EBS, PeopleSoft e JDE da Galeria

Para configurar a integração do SSOGEN-Azure AD SSO gateway para Oracle E-Business Suite-EBS, PeopleSoft e JDE no Azure AD, você precisa adicionar o SSOGEN-Azure AD SSO gateway para Oracle E-Business Suite-EBS, PeopleSoft e JDE da Galeria à sua lista de S gerenciados aplicativos da aaS.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **SSOGEN-Azure ad SSO gateway para Oracle E-Business Suite-EBS, PeopleSoft e JDE** na caixa de pesquisa.
1. Selecione **SSOGEN-Azure ad SSO gateway for Oracle E-Business Suite-EBS, PeopleSoft e JDE** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde"></a>Configurar e testar o logon único do Azure AD para SSOGEN-Azure AD SSO gateway para Oracle E-Business Suite-EBS, PeopleSoft e JDE

Configure e teste o SSO do Azure AD com o SSOGEN-Azure AD SSO gateway para Oracle E-Business Suite-EBS, PeopleSoft e JDE usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no SSOGEN-Azure AD SSO gateway para Oracle E-Business Suite-EBS, PeopleSoft e JDE.

Para configurar e testar o SSO do Azure AD com o SSOGEN-Azure AD SSO gateway para Oracle E-Business Suite-EBS, PeopleSoft e JDE, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o gateway de SSO do Azure ad para Oracle E Business Suite EBS, PeopleSoft e JDE SSO](#configure-ssogen-azure-ad-sso-gateway-for-oracle-e-business-suite-ebs-peoplesoft-and-jde-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do SSOGEN Azure ad SSO gateway para Oracle E Business Suite EBS, PeopleSoft e JDE](#create-ssogen-azure-ad-sso-gateway-for-oracle-e-business-suite-ebs-peoplesoft-and-jde-test-user)** – para ter um equivalente de B. Simon em SSOGEN-Azure ad SSO gateway para Oracle E-Business Suite-EBS, PEOPLESOFT e JDE que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **SSOGEN-Azure ad SSO para Oracle E-Business Suite-EBS, PeopleSoft e JDE** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , insira os valores para os seguintes campos:

    Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https://<customer_name>.ssogen.com/ssogen/login?client_name=<customer_name>`

1. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<customer_name>.ssogen.com/ssogen/login?client_name=<customer_name>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de resposta e a URL de logon reais. Entre em contato com o [SSOGEN-Azure ad SSO gateway for Oracle E-Business Suite-EBS, PeopleSoft e JDE Client Support Team](mailto:support@ssogen.com) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. Seu aplicativo SSOGEN-Azure AD SSO gateway para Oracle E-Business Suite-EBS, PeopleSoft e JDE espera que as asserções SAML estejam em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão, onde o **nameidentifier** é mapeado com **User. UserPrincipalName**. SSOGEN-o gateway de SSO do Azure AD para Oracle E-Business Suite – o aplicativo EBS, PeopleSoft e JDE espera que **nameidentifier** seja mapeado com **User. onpremisessamaccountname**, portanto, você precisa editar o mapeamento de atributo clicando no ícone **Editar** e alterar o mapeamento de atributo.

    ![imagem](common/edit-attribute.png)

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao SSOGEN-Azure AD SSO gateway para Oracle E-Business Suite-EBS, PeopleSoft e JDE.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **SSOGEN-Azure ad SSO gateway for Oracle E-Business Suite-EBS, PeopleSoft e JDE**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-ssogen-azure-ad-sso-gateway-for-oracle-e-business-suite-ebs-peoplesoft-and-jde-sso"></a>Configurar o gateway de SSO do Azure AD para Oracle E Business Suite SSOGEN, PeopleSoft e SSO do JDE

Para configurar o logon único no **SSOGEN-Azure ad SSO gateway para Oracle E-Business Suite – EBS, PeopleSoft e JDE** Side, encontre a documentação de registro de SSO específico do aplicativo abaixo:

* Oracle EBS-integração de SSO do Azure AD: [https://www.ssogen.com/oracle-ebs-sso-ldap/](https://www.ssogen.com/oracle-ebs-sso-ldap/)
* PeopleSoft-integração de SSO do Azure AD: [https://www.ssogen.com/peoplesoft-sso/](https://www.ssogen.com/peoplesoft-sso/)
* JD Edwards-integração de SSO do Azure AD: [https://www.ssogen.com/oracle-jde-sso/](https://www.ssogen.com/oracle-jde-sso/)
* Integração de SSO do Apache-Azure AD: [https://www.ssogen.com/apache-sso-authentication/](https://www.ssogen.com/apache-sso-authentication/)

### <a name="create-ssogen-azure-ad-sso-gateway-for-oracle-e-business-suite-ebs-peoplesoft-and-jde-test-user"></a>Criar SSOGEN do gateway de SSO do Azure AD para Oracle E Business Suite EBS, PeopleSoft e JDE usuário de teste

O Azure AD envia o identificador de usuário exclusivo (ID de nome) para o aplicativo de usuário, depois que a autenticação for bem-sucedida.  Certifique-se de que o identificador de usuário exclusivo (ID de nome) corresponda ao registro de usuário em seu aplicativo, FND_USER. USER_NAME no Oracle EBS, por exemplo.

Entre em contato com [info@ssogen.com](mailto:info@ssogen.com) e [support@ssogen.com](mailto:support@ssogen.com) para qualquer suporte.

## <a name="test-sso"></a>Testar SSO

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco SSOGEN-Azure AD SSO gateway para Oracle E-Business Suite-EBS, PeopleSoft e JDE no painel de acesso, você deverá ser conectado automaticamente ao SSOGEN-Azure AD SSO gateway for Oracle E-Business Suite-EBS, PeopleSoft e JDE para os quais você criar SSO do et. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o SSOGEN-gateway de SSO do Azure AD para Oracle E-Business Suite-EBS, PeopleSoft e JDE com o Azure AD](https://aad.portal.azure.com/)

- [O que é o controle de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger o SSOGEN-Azure AD SSO gateway para Oracle E-Business Suite-EBS, PeopleSoft e JDE com visibilidade e controles avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
