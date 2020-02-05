---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com a Nintex Promapp  Azure Ative Diretório) integração com a Nintex Promapp  Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Nintex Promapp.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 418d0601-6e7a-4997-a683-73fa30a2cfb5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/30/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 581c850801c153996031378cbf470457264cad3d
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76984478"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nintex-promapp"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com a Nintex Promapp

Neste tutorial, você vai aprender a integrar o Nintex Promapp com o Azure Ative Directory (Azure AD). Quando integrar o Nintex Promapp com o Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Nintex Promapp.
* Permita que os seus utilizadores sejam automaticamente inscritos no Nintex Promapp com as suas contas Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Nintex Promapp única subscrição ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* Nintex Promapp apoia **SP e IDP** iniciadoS SSO
* Nintex Promapp suporta o fornecimento de utilizadores **justo no tempo**

## <a name="adding-nintex-promapp-from-the-gallery"></a>Adicionando Nintex Promapp da galeria

Para configurar a integração do Nintex Promapp no Azure AD, você precisa adicionar Nintex Promapp da galeria à sua lista de aplicações geridas saaS.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. No Add da secção **da galeria,** escreva **Nintex Promapp** na caixa de pesquisa.
1. Selecione **Nintex Promapp** no painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-nintex-promapp"></a>Configure e teste Azure AD single sign-on para Nintex Promapp

Configure e teste Azure AD SSO com Nintex Promapp utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Nintex Promapp.

Para configurar e testar o Azure AD SSO com o Nintex Promapp, complete os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configure Nintex Promapp SSO](#configure-nintex-promapp-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Crie o utilizador de teste Nintex Promapp](#create-nintex-promapp-test-user)** - para ter uma contrapartida de B.Simon no Nintex Promapp que está ligado à representação do utilizador da AD Azure.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **nintex Promapp,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , insira os valores para os seguintes campos:

    1. Na caixa **de identificador,** introduza um URL neste padrão:

        | |
        |--|
        | `https://go.promapp.com/TENANTNAME/`|
        | `https://au.promapp.com/TENANTNAME/`|
        | `https://us.promapp.com/TENANTNAME/`|
        | `https://eu.promapp.com/TENANTNAME/`|
        | `https://ca.promapp.com/TENANTNAME/`|
        |   |

       > [!NOTE]
       > A integração da Azure AD com a Nintex Promapp está atualmente configurada apenas para autenticação iniciada pelo serviço. (Isto é, ir a um URL nintex Promapp inicia o processo de autenticação.) Mas o campo URL de **resposta** é um campo necessário.

    1. Na caixa **DEURL resposta,** introduza um URL neste padrão:

       `https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate.aspx`

1. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    Na caixa **de URL Sign on,** introduza um URL neste padrão: `https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate`

    > [!NOTE]
    > Estes valores são espaços reservados. Tem de utilizar o url do identificador real, resposta e URL de inscrição. Contacte a equipa de apoio da [Nintex Promapp](https://www.promapp.com/about-us/contact-us/) para obter os valores. Também pode consultar os padrões mostrados na caixa de diálogo **Basic SAML Configuration** no portal Azure.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/certificatebase64.png)

1. Na secção Configurar o **Nintex Promapp,** copie os URL(s) adequados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use o único sign-on Azure, concedendo acesso ao Nintex Promapp.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **Nintex Promapp**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-nintex-promapp-sso"></a>Configure Nintex Promapp SSO

1. Inscreva-se no site da empresa Nintex Promapp como administrador.

2. No menu na parte superior da janela, selecione **Administrador:**

    ![Selecione Administrador][12]

3. **Selecione Configurar:**

    ![Selecione Configurar][13]

4. Na caixa de diálogo **de segurança,** tome os seguintes passos.

    ![Caixa de diálogo de segurança][14]

    1. Colhe o URL de **Login** que copiou do portal Azure para a caixa **URL SSO-Login.**

    1. Na lista **SSO - Modo de Inscrição Única,** selecione **Opcional**. Selecione **Guardar**.

       > [!NOTE]
       > O modo opcional é apenas para testar. Depois de ficar satisfeito com a configuração, selecione **Required** na lista **sso - modo de início de sessão único** para forçar todos os utilizadores a autenticar com AD Azure.

    1. No Notepad, abra o certificado que descarregou na secção anterior. Copiar o conteúdo do certificado sem a primeira linha **(-----BEGIN CERTIFICATE-----** ) ou a última linha **(-----END CERTIFICATE-----** ). Colhe o conteúdo do certificado na caixa de **certificado SSO-x.509** e, em seguida, selecione **Guardar**.

### <a name="create-nintex-promapp-test-user"></a>Criar o utilizador de teste Nintex Promapp

Nesta secção, um utilizador chamado B.Simon é criado no Nintex Promapp. A Nintex Promapp suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para você nesta seção. Se um utilizador já não existir no Nintex Promapp, um novo é criado após a autenticação.

## <a name="test-sso"></a>Testar SSO

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clicar no azulejo Nintex Promapp no Painel de Acesso, deve ser automaticamente inscrito no Nintex Promapp para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Nintex Promapp com a Azure AD](https://aad.portal.azure.com/)

<!--Image references-->

[12]: ./media/promapp-tutorial/tutorial_promapp_05.png
[13]: ./media/promapp-tutorial/tutorial_promapp_06.png
[14]: ./media/promapp-tutorial/tutorial_promapp_07.png