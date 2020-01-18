---
title: 'Tutorial: Azure Active Directory integração de SSO (logon único) com o Workplace by Facebook | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Workplace by Facebook.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/07/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3252b7b257fda96b3d711c5f47ec7c6eb7ee36cb
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76262197"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workplace-by-facebook"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o Workplace by Facebook

Neste tutorial, você aprenderá a integrar o Workplace by Facebook ao Azure Active Directory (Azure AD). Ao integrar o Workplace by Facebook ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Workplace by Facebook.
* Habilite seus usuários a entrarem automaticamente no workplace by Facebook com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do workplace by Facebook.

> [!NOTE]
> O Facebook tem dois produtos, o local de trabalho Standard (gratuito) e o Workplace Premium (pago). Qualquer locatário do workplace Premium pode configurar a integração de SCIM e SSO sem outras implicações ao custo ou às licenças necessárias. SSO e SCIM não estão disponíveis em instâncias padrão do workplace.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* Workplace by Facebook dá suporte ao SSO iniciado pelo **SP**
* O Workplace by Facebook dá suporte ao **provisionamento just-in-time**
* Workplace by Facebook dá suporte ao  **[provisionamento automático de usuário](workplacebyfacebook-provisioning-tutorial.md)**
* O aplicativo móvel do workplace by Facebook agora pode ser configurado com o Azure AD para habilitar o SSO. Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Adicionando o Workplace by Facebook por meio da Galeria

Para configurar a integração do workplace by Facebook ao Azure AD, você precisará adicionar o Workplace by Facebook da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **Workplace by Facebook** na caixa de pesquisa.
1. Selecione **Workplace by Facebook** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-workplace-by-facebook"></a>Configurar e testar o SSO do Azure AD para o Workplace by Facebook

Configure e teste o SSO do Azure AD com o Workplace by Facebook usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no workplace by Facebook.

Para configurar e testar o SSO do Azure AD com o Workplace by Facebook, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
2. **[Configurar o local de trabalho pelo SSO do Facebook](#configure-workplace-by-facebook-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do workplace by Facebook](#create-workplace-by-facebook-test-user)** – para ter um equivalente de B. Simon no workplace by Facebook que esteja vinculado à representação do usuário no Azure AD.
3. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Workplace by Facebook** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<instancename>.facebook.com`

    b. Na caixa de texto **identificador (ID da entidade)** , digite uma URL usando o seguinte padrão: `https://www.facebook.com/company/<instanceID>`

    c. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https://www.facebook.com/company/<instanceID>`

    > [!NOTE]
    > Esses valores não são o real. Atualize esses valores com a URL de logon, o identificador e a URL de resposta reais. Consulte a página autenticação do painel da empresa do workplace para obter os valores corretos para sua comunidade de local de trabalho. isso é explicado posteriormente no tutorial.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/certificatebase64.png)

1. Na seção **Configurar o local de trabalho por Facebook** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao Workplace by Facebook.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Workplace by Facebook**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-workplace-by-facebook-sso"></a>Configurar local de trabalho por SSO do Facebook

1. Para automatizar a configuração no local de trabalho pelo Facebook, você precisa instalar a **extensão do navegador de entrada seguro de meus aplicativos** clicando em **instalar a extensão**.

    ![Extensão de meus aplicativos](common/install-myappssecure-extension.png)

1. Depois de adicionar a extensão ao navegador, clique em **Configurar o Workplace by Facebook** irá direcioná-lo para o aplicativo Workplace by Facebook. A partir daí, forneça as credenciais de administrador para entrar no workplace by Facebook. A extensão do navegador irá configurar automaticamente o aplicativo para você e automatizar as etapas de 3-5.

    ![Configuração da instalação](common/setup-sso.png)

1. Se você quiser configurar o Workplace by Facebook manualmente, abra uma nova janela do navegador da Web e entre no site da empresa do workplace by Facebook como administrador e execute as seguintes etapas:

    > [!NOTE]
    > Como parte do processo de autenticação SAML, o Workplace pode utilizar cadeias de caracteres de consulta de até 2,5 quilobytes de tamanho para passar parâmetros para o Azure AD.

1. No painel de navegação esquerdo, navegue até a guia **segurança** > **autenticação** .

    ![Painel de administração](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure01.png)

    a. Verifique a opção de **logon único (SSO)** .
    
    b. Clique em **+ Adicionar novo provedor de SSO**.

1. Na guia **autenticação** , selecione **SSO (logon único)** e execute as seguintes etapas:

    ![Guia Autenticação](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure02.png)

    a. No **nome do provedor de SSO**, insira o nome da instância de SSO, como Azureadsso.

    b. Na caixa de texto **URL do SAML** , Cole o valor da **URL de logon**copiado de portal do Azure.

    c. Na caixa de texto **URL do emissor SAML** , Cole o valor do **identificador do Azure ad**que você copiou do portal do Azure.

    d. Abra seu **certificado codificado em base 64** no bloco de notas baixado de portal do Azure, copie o conteúdo dele para a área de transferência e cole-o na caixa de texto **certificado SAML** .

    e. Copie a **URL da audiência** da sua instância e cole-a na caixa de texto **identificador (ID da entidade)** na seção **configuração básica do SAML** em portal do Azure.

    f. Copie a **URL do destinatário** para sua instância e cole-a na caixa de texto **URL de logon** na seção **configuração básica do SAML** em portal do Azure.

    g. Copie a **URL do ACS (serviço de consumidor de asserção)** para sua instância e cole-a na caixa de texto **URL de resposta** na seção **configuração básica do SAML** em portal do Azure.

    h. Role até a parte inferior da seção e clique no botão **testar SSO** . Isso resulta em uma janela pop-up que aparece com a página de logon do Azure AD apresentada. Insira suas credenciais em normalmente para autenticar.

    **Solução de problemas:** Verifique se o endereço de email retornado do Azure AD é o mesmo da conta do local de trabalho com a qual você está conectado.

    i. Depois que o teste tiver sido concluído com êxito, role até a parte inferior da página e clique no botão **salvar** .

    j. Todos os usuários que usam o local de trabalho agora serão apresentados com a página de logon do Azure AD para autenticação.

1. **Redirecionamento de logout SAML (opcional)**  -

    Você pode optar por configurar opcionalmente uma URL de logout SAML, que pode ser usada para apontar para a página de logout do Azure AD. Quando essa configuração estiver habilitada e configurada, o usuário não será mais direcionado para a página de logout do local de trabalho. Em vez disso, o usuário será redirecionado para a URL que foi adicionada na configuração de redirecionamento de logout SAML.

### <a name="configuring-reauthentication-frequency"></a>Configurando a frequência de reautenticação

Você pode configurar o local de trabalho para solicitar uma verificação SAML todos os dias, três dias, semana, duas semanas, mês ou nunca.

> [!NOTE]
> O valor mínimo para a verificação SAML em aplicativos móveis é definido como uma semana.

Você também pode forçar uma redefinição de SAML para todos os usuários usando o botão: exigir autenticação SAML para todos os usuários agora.

### <a name="create-workplace-by-facebook-test-user"></a>Criar um usuário de teste do workplace by Facebook

Nesta seção, um usuário chamado B. Simon é criado no workplace by Facebook. O Workplace by Facebook dá suporte ao provisionamento just-in-time, que é habilitado por padrão.

Não há nenhuma ação para você nesta seção. Se um usuário não existir no workplace by Facebook, um novo será criado quando você tentar acessar o Workplace by Facebook.

>[!Note]
>Se você precisar criar um usuário manualmente, entre em contato com a [equipe de suporte ao cliente do workplace by Facebook](https://workplace.fb.com/faq/)

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco Workplace by Facebook no painel de acesso, você deverá entrar automaticamente no workplace by Facebook para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="test-sso-for-workplace-by-facebook-mobile"></a>Testar SSO para Workplace by Facebook (Mobile)

1. Abra o local de trabalho pelo aplicativo móvel do Facebook. Na página de entrada, clique em **fazer logon**.

    ![A entrada](./media/workplacebyfacebook-tutorial/test05.png)

2. Insira seu email comercial e clique em **continuar**.

    ![O email](./media/workplacebyfacebook-tutorial/test02.png)

3. Clique em **apenas uma vez**.

    ![Uma vez](./media/workplacebyfacebook-tutorial/test04.png)

4. Clique em **Permitir**.

    ![O permitir](./media/workplacebyfacebook-tutorial/test03.png)

5. Por fim, após entrar com êxito, a home page do aplicativo será exibida.    

    ![A Home Page](./media/workplacebyfacebook-tutorial/test01.png)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configurar provisionamento de usuário](workplacebyfacebook-provisioning-tutorial.md)

- [Experimente o Workplace by Facebook com o Azure AD](https://aad.portal.azure.com)
