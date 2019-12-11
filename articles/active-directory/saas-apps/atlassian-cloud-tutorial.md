---
title: 'Tutorial: integração do Azure Active Directory com o Atlassian Cloud | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Atlassian Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: celested
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3a54b096019e9e38bc800ae313016a430062dab
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74964332"
---
# <a name="tutorial-integrate-atlassian-cloud-with-azure-active-directory"></a>Tutorial: integrar o Atlassian Cloud ao Azure Active Directory

Neste tutorial, você aprenderá a integrar o Atlassian Cloud ao Azure Active Directory (Azure AD). Ao integrar o Atlassian Cloud ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Atlassian Cloud.
* Habilite seus usuários a serem conectados automaticamente ao Atlassian Cloud com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* Assinatura habilitada para SSO (logon único) do Atlassian Cloud.
* Para habilitar o logon único do Security Assertion Markup Language (SAML) para produtos Atlassian Cloud, você precisa configurar o acesso do Atlassian. Saiba mais sobre o [acesso Atlassian]( https://www.atlassian.com/enterprise/cloud/identity-manager).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. 

* O Atlassian Cloud é compatível com o SSO iniciado por **SP e IDP**
* O Atlassian Cloud dá suporte ao [provisionamento e desprovisionamento automáticos de usuário](atlassian-cloud-provisioning-tutorial.md)

## <a name="adding-atlassian-cloud-from-the-gallery"></a>Adicionando o Atlassian Cloud da Galeria

Para configurar a integração do Atlassian Cloud ao Azure AD, você precisará adicionar o Atlassian Cloud da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **Atlassian Cloud** na caixa de pesquisa.
1. Selecione **Atlassian Cloud** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configure e teste o SSO do Azure AD com o Atlassian Cloud usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Atlassian Cloud.

Para configurar e testar o SSO do Azure AD com o Atlassian Cloud, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar SSO de nuvem do Atlassian](#configure-atlassian-cloud-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    * **[Criar usuário de teste do Atlassian Cloud](#create-atlassian-cloud-test-user)** – para ter um equivalente de B. Simon no Atlassian Cloud que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Atlassian Cloud** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , insira os valores para os seguintes campos:

    a. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão: `https://auth.atlassian.com/saml/<unique ID>`

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`

    c. Clique em **definir URLs adicionais**.

    d. Na caixa de texto **estado de retransmissão** , digite uma URL usando o seguinte padrão: `https://<instancename>.atlassian.net`

    > [!NOTE]
    > Os valores anteriores não são reais. Atualize esses valores com o identificador e a URL de resposta reais. Você obterá esses valores reais na tela de **configuração do Atlassian Cloud SAML** , que é explicada posteriormente no tutorial **Configurar o logon único do Atlassian Cloud** .

1. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<instancename>.atlassian.net`

    > [!NOTE]
    > O valor da URL de logon não é real. Cole o valor da instância do que você usa para entrar no portal de administração do Atlassian Cloud.

    ![Configurar o início de sessão único](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-10.png)

1. Seu aplicativo Atlassian Cloud espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão, onde o **nameidentifier** é mapeado com **User. UserPrincipalName**. O aplicativo Atlassian Cloud espera que o **nameidentifier** seja mapeado com **User. mail**, portanto, você precisa editar o mapeamento de atributo clicando no ícone **Editar** e alterar o mapeamento de atributo.

    ![imagem](common/edit-attribute.png)

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/certificatebase64.png)

1. Na seção **Configurar a nuvem do Atlassian** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao Atlassian Cloud.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Atlassian Cloud**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-atlassian-cloud-sso"></a>Configurar SSO de nuvem do Atlassian

1. Para automatizar a configuração no Atlassian Cloud, você precisa instalar a **extensão do navegador de entrada seguro de meus aplicativos** clicando em **instalar a extensão**.

    ![Extensão de meus aplicativos](common/install-myappssecure-extension.png)

1. Depois de adicionar a extensão ao navegador, clique em **Configurar o Atlassian Cloud** irá direcioná-lo para o aplicativo Atlassian Cloud. A partir daí, forneça as credenciais de administrador para entrar no Atlassian Cloud. A extensão do navegador irá configurar automaticamente o aplicativo para você e automatizar as etapas de 3-7.

    ![Configuração da instalação](common/setup-sso.png)

1. Se você quiser configurar o Atlassian Cloud manualmente, abra uma nova janela do navegador da Web e entre no site da empresa do Atlassian Cloud como administrador e execute as seguintes etapas:

1. Você precisa verificar seu domínio antes de configurar o logon único. Para obter mais informações, consulte [Atlassian Domain Verification](https://confluence.atlassian.com/cloud/domain-verification-873871234.html) Document.

1. No painel esquerdo, selecione **segurança** > **logon único do SAML**. Se você ainda não fez isso, assine o Atlassian Identity Manager.

    ![Configurar o início de sessão único](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-11.png)

1. Na janela **Adicionar configuração do SAML** , faça o seguinte:

    ![Configurar o início de sessão único](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-12.png)

    a. Na caixa **ID da entidade do provedor de identidade** , Cole o **identificador do Azure ad** que você copiou do portal do Azure.

    b. Na caixa **URL de SSO do provedor de identidade** , Cole a **URL de logon** que você copiou do portal do Azure.

    c. Abra o certificado baixado do portal do Azure em um arquivo. txt, copie o valor (sem as linhas *begin Certificate* e *end Certificate* ) e cole-o na caixa **certificado X509 público** .

    d. Clique em **salvar configuração**.

1. Para garantir que você tenha configurado as URLs corretas, atualize as configurações do Azure AD fazendo o seguinte:

    ![Configurar o início de sessão único](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-13.png)

    a. Na janela SAML, copie a **ID de identidade do SP** e, na portal do Azure, em configuração de SAML do Atlassian Cloud **Basic**, Cole-a na caixa **identificador** .

    b. Na janela SAML, copie a **URL do serviço do consumidor de asserção do SP** e, na portal do Azure, em configuração de SAML do Atlassian Cloud **Basic**, Cole-a na caixa **URL de resposta** . A URL de logon é a URL do locatário de sua nuvem do Atlassian.

    > [!NOTE]
    > Se você for um cliente existente, depois de atualizar a **ID da identidade do SP** e os valores da **URL do serviço do consumidor de asserção do SP** na portal do Azure, selecione **Sim, atualizar configuração**. Se você for um novo cliente, poderá ignorar esta etapa.

### <a name="create-atlassian-cloud-test-user"></a>Criar usuário de teste do Atlassian Cloud

Para permitir que os usuários do Azure AD entrem no Atlassian Cloud, provisione as contas de usuário manualmente no Atlassian Cloud fazendo o seguinte:

1. No painel **Administração** , selecione **usuários**.

    ![O link de usuários de nuvem do Atlassian](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-14.png)

1. Para criar um usuário no Atlassian Cloud, selecione **convidar usuário**.

    ![Criar um usuário de nuvem do Atlassian](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-15.png)

1. Na caixa **endereço de email** , insira o endereço de email do usuário e atribua o acesso ao aplicativo.

    ![Criar um usuário de nuvem do Atlassian](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-16.png)

1. Para enviar um convite por email ao usuário, selecione **convidar usuários**. Um convite por email é enviado ao usuário e, depois de aceitar o convite, o usuário está ativo no sistema.

> [!NOTE]
> Você também pode criar usuários em massa selecionando o botão **criação em massa** na seção **usuários** .

### <a name="test-sso"></a>Testar SSO

Ao selecionar o bloco do Atlassian Cloud no painel de acesso, você deverá ser conectado automaticamente à nuvem do Atlassian para a qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Atlassian Cloud com o Azure AD](https://aad.portal.azure.com/)
