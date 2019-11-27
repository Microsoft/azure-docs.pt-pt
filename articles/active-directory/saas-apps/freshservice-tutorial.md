---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o Freshservice | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Freshservice.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3dd22b1f-445d-45c6-8eda-30207eb9a1a8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e08ef72dca09f873ad1cfcc91e132063b88406b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227524"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshservice"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o Freshservice

Neste tutorial, você aprenderá a integrar o Freshservice com o Azure Active Directory (Azure AD). Ao integrar o Freshservice ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Freshservice.
* Habilite seus usuários a serem conectados automaticamente ao Freshservice com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Freshservice.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Freshservice dá suporte ao SSO iniciado por **SP**

## <a name="adding-freshservice-from-the-gallery"></a>Adicionando o Freshservice da Galeria

Para configurar a integração do Freshservice ao Azure AD, você precisará adicionar o Freshservice da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **Freshservice** na caixa de pesquisa.
1. Selecione **Freshservice** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-freshservice"></a>Configurar e testar o logon único do Azure AD para o Freshservice

Configure e teste o SSO do Azure AD com o Freshservice usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Freshservice.

Para configurar e testar o SSO do Azure AD com o Freshservice, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do Freshservice](#configure-freshservice-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do Freshservice](#create-freshservice-test-user)** – para ter um equivalente de B. Simon em Freshservice que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Freshservice** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<democompany>.freshservice.com`

    b. Na caixa de texto **identificador (ID da entidade)** , digite uma URL usando o seguinte padrão: `https://<democompany>.freshservice.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de entrada e o identificador reais. Contate a [equipe de suporte ao cliente do Freshservice](https://support.freshservice.com/) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/certificatebase64.png)

1. O Freshservice requer impressão digital SHA-256 para que o SSO funcione. Para obter a impressão digital do SHA-256, execute as seguintes etapas:

    ![Impressão digital](./media/freshservice-tutorial/ic790821.png "Impressão digital")

    1. Abra o [link](https://www.samltool.com/fingerprint.php) em um navegador da Web diferente.

    1. Abra o arquivo de certificado (Base64) baixado no bloco de notas e cole o conteúdo na caixa de texto **certificado X. 509** .

    1. Para o algoritmo, selecione **SHA256** na lista suspensa.

    1. Clique em **calcular impressão digital**.

    1. Clique no ícone de cópia para copiar a **impressão digital** gerada e salvá-la em seu computador.

1. Na seção **Configurar Freshservice** na **portal do Azure**, copie as URLs apropriadas com base em seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B. Simon.

1. No painel esquerdo na portal do Azure, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.
1. Selecione **novo usuário** na parte superior da tela.
1. Nas propriedades do **usuário** , siga estas etapas:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome de usuário** , insira o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **senha** .
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao Freshservice.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Freshservice**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-freshservice-sso"></a>Configurar o SSO do Freshservice

1. Para automatizar a configuração no Freshservice, você precisa instalar a **extensão do navegador de entrada seguro de meus aplicativos** clicando em **instalar a extensão**.

    ![Extensão de meus aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, clique em **Setup Freshservice** irá direcioná-lo para o aplicativo Freshservice. A partir daí, forneça as credenciais de administrador para entrar no Freshservice. A extensão do navegador irá configurar automaticamente o aplicativo para você e automatizar as etapas de 3-6.

    ![Configuração da instalação](common/setup-sso.png)

3. Se você quiser configurar o Freshservice manualmente, abra uma nova janela do navegador da Web e entre no site da empresa do Freshservice como administrador e execute as seguintes etapas:

4. No menu na parte superior, clique em **admin**.

    ![ADM](./media/freshservice-tutorial/ic790814.png "administrador")

5. No **portal do cliente**, clique em **segurança**.

    ![Segurança](./media/freshservice-tutorial/ic790815.png "Segurança")

6. Na seção **segurança** , execute as seguintes etapas:

    ![Início de Sessão Único](./media/freshservice-tutorial/ic790816.png "Início de Sessão Único")

    a. Alterne o **logon único**.

    b. Selecione **SSO do SAML**.

    c. Na caixa de texto **URL de logon do SAML** , Cole o valor da URL de **logon**copiado de portal do Azure.

    d. Na caixa de texto **URL de logout** , Cole o valor da **URL de logout**copiado de portal do Azure.

    e. Na caixa de texto **impressão digital do certificado de segurança** , Cole o valor da **impressão digital** que você gerou anteriormente.

    f. Clicar em **Guardar**

### <a name="create-freshservice-test-user"></a>Criar usuário de teste do Freshservice

Para permitir que os usuários do Azure AD entrem no FreshService, eles devem ser provisionados no FreshService. No caso do FreshService, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no site da empresa do **FreshService** como um administrador.

2. No menu na parte superior, clique em **admin**.

    ![ADM](./media/freshservice-tutorial/ic790814.png "administrador")

3. Na seção **Gerenciamento de usuário** , clique em **solicitantes**.

    ![Os solicitantes](./media/freshservice-tutorial/ic790818.png "Os solicitantes")

4. Clique em **novo solicitante**.

    ![Novos solicitantes](./media/freshservice-tutorial/ic790819.png "Novos solicitantes")

5. Na seção **novo solicitante** , execute as seguintes etapas:

    ![Novo solicitante](./media/freshservice-tutorial/ic790820.png "Novo solicitante")  

    a. Insira o **nome** e os atributos de **email** de uma conta de Azure Active Directory válida que você deseja provisionar nas caixas de correio relacionadas.

    b. Clique em **Guardar**.

    > [!NOTE]
    > O titular da conta Azure Active Directory recebe um email que inclui um link para confirmar a conta antes que ela se torne ativa
    >  

> [!NOTE]
> Você pode usar qualquer outra ferramenta de criação de conta de usuário FreshService ou APIs fornecidas pelo FreshService para provisionar contas de usuário do Azure AD.

## <a name="test-sso"></a>Testar SSO

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do Freshservice no painel de acesso, você deverá ser conectado automaticamente ao Freshservice para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Freshservice com o Azure AD](https://aad.portal.azure.com/)