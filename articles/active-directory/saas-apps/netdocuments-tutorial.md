---
title: 'Tutorial: Azure Active Directory integração de SSO (logon único) com o netdocuments | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o netdocuments.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1a47dc42-1a17-48a2-965e-eca4cfb2f197
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0bf25ce318415a8aa36bca8d4cd7380e4e8e67ca
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70743460"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netdocuments"></a>Tutorial: Azure Active Directory integração de SSO (logon único) com o netdocuments

Neste tutorial, você aprenderá a integrar o netdocuments ao Azure Active Directory (Azure AD). Ao integrar o netdocuments ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao netdocuments.
* Habilite seus usuários a serem conectados automaticamente ao netdocuments com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do netdocuments.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O netdocuments dá suporte ao SSO iniciado por **SP**

## <a name="adding-netdocuments-from-the-gallery"></a>Adicionando o netdocuments da Galeria

Para configurar a integração do netdocuments ao Azure AD, você precisa adicionar o netdocuments da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **netdocuments** na caixa de pesquisa.
1. Selecione **Netdocuments** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netdocuments"></a>Configurar e testar o logon único do Azure AD para o netdocuments

Configure e teste o SSO do Azure AD com o netdocuments usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no netdocuments.

Para configurar e testar o SSO do Azure AD com o netdocuments, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do Netdocuments](#configure-netdocuments-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Netdocuments](#create-netdocuments-test-user)** – para ter um equivalente de B. Simon em netdocuments que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **netdocuments** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão:`https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão:`https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de entrada e a URL de resposta reais. A ID do repositório é um valor que começa com **CA-** seguido por 8 códigos de caracteres associados ao repositório do netdocuments. Você pode verificar o [documento suporte a identidade federada do Netdocuments](https://support.netdocuments.com/hc/en-us/articles/205220410-Federated-Identity-Login) para obter mais informações. Como alternativa, você pode entrar em contato com a [equipe de suporte ao cliente do Netdocuments](https://support.netdocuments.com/hc/) para obter esses valores se tiver dificuldades com a configuração usando as informações acima. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. O aplicativo netdocuments espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão, onde o **nameidentifier** é mapeado com **User. UserPrincipalName**. O aplicativo netdocuments espera que **nameidentifier** seja mapeado com **EmployeeID** ou qualquer outra declaração que seja aplicável à sua organização como **nameidentifier**, portanto, você precisa editar o mapeamento de atributo clicando no ícone **Editar** e alterar o mapeamento de atributo.

    ![image](common/edit-attribute.png)

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Na seção **Configurar o Netdocuments** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao netdocuments.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Netdocuments**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-netdocuments-sso"></a>Configurar o SSO do netdocuments

1. Em uma janela diferente do navegador da Web, entre no site da empresa do netdocuments como um administrador.

2. Vá para **administrador**.

3. Clique em **Adicionar e remover usuários e grupos**.
   
    ![Repositório] do (./media/netdocuments-tutorial/ic795047.png "Repositório") do

4. Clique em **Configurar opções de autenticação avançadas**.
    
    ![Configurar opções de autenticação avançadas](./media/netdocuments-tutorial/ic795048.png "Configurar opções de autenticação avançadas")

5. Na caixa de diálogo **identidade federada** , execute as seguintes etapas:
   
    ![Identidade federada](./media/netdocuments-tutorial/ic795049.png "Identidade federada")
   
    a. Como **tipo de servidor de identidade federada**, selecione **serviços de Federação do Active Directory (AD FS)** .
   
    b. Clique em **escolher arquivo**para carregar o arquivo de metadados baixado que você baixou de portal do Azure.
   
    c. Clique em **OK**.

### <a name="create-netdocuments-test-user"></a>Criar usuário de teste do netdocuments

Para permitir que os usuários do Azure AD entrem no netdocuments, eles devem ser provisionados no netdocuments.  
No caso do netdocuments, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do **Netdocuments** como administrador.

2. No menu na parte superior, clique em **admin**.
   
    ![Administrador] do (./media/netdocuments-tutorial/ic795051.png "Administrador") do

3. Clique em **Adicionar e remover usuários e grupos**.
   
    ![Repositório] do (./media/netdocuments-tutorial/ic795047.png "Repositório") do

4. Na caixa de texto **endereço de email** , digite o endereço de email de uma conta de Azure Active Directory válida que você deseja provisionar e clique em **Adicionar usuário**.
   
    ![Endereço de email](./media/netdocuments-tutorial/ic795053.png "Endereço de email")
   
    >[!NOTE]
    >O titular da conta Azure Active Directory receberá um email que inclui um link para confirmar a conta antes que ela se torne ativa. Você pode usar qualquer outra ferramenta de criação de conta de usuário do netdocuments ou APIs fornecidas pelo netdocuments para provisionar Azure Active Directory contas de usuário.

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco netdocuments no painel de acesso, você deverá ser conectado automaticamente aos documentos do NETpara os quais você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o netdocuments com o Azure AD](https://aad.portal.azure.com/)

