---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com 8x8 [ Integração de entrada única de diretório ativo azure) com 8x8 [ 8x8 ] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o 8x8.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b34a6edf-e745-4aec-b0b2-7337473d64c5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/20/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c598222978a1c831be6f5e9db9eb87b2d6b6b96
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "78968653"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-8x8"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com 8x8

Neste tutorial, você vai aprender a integrar o 8x8 com o Azure Ative Directory (Azure AD). Quando integrar o 8x8 com o Azure AD, pode:

* Controlo em Azure AD que tem acesso ao 8x8.
* Ative que os seus utilizadores sejam automaticamente inscritos no 8x8 com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma assinatura 8x8.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* 8x8 suporta **SP e IDP** iniciadoS SSO

* Uma vez configurado o 8x8 pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia, pelo que apenas uma instância pode ser configurada num inquilino.

## <a name="adding-8x8-from-the-gallery"></a>Adicionando 8x8 da galeria

Para configurar a integração do 8x8 em Azure AD, precisa adicionar 8x8 da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** tipo **8x8** na caixa de pesquisa.
1. Selecione **8x8** do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-8x8"></a>Configure e teste Azure AD único sinal para 8x8

Configure e teste Azure AD SSO com 8x8 utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em 8x8.

Para configurar e testar o Azure AD SSO com 8x8, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure 8x8 SSO](#configure-8x8-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. Crie um utilizador de **[teste 8x8](#create-8x8-test-user)** - para ter uma contraparte de B.Simon em 8x8 que esteja ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **8x8,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    a. Na caixa de texto **identificador,** digite um URL:`https://sso.8x8.com/saml2`

    b. Na caixa de texto **URL resposta,** escreva um URL:`https://sso.8x8.com/saml2`

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador. Utilizará o certificado mais tarde no tutorial na secção **Configure 8x8 SSO.**

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção **Configurar 8x8,** copiar os URL(s) e utilizarestes valores de URL mais tarde no tutorial.

    ![URLs de configuração de cópia](./media/8x8virtualoffice-tutorial/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso ao 8x8.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **8x8**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-8x8-sso"></a>Configure 8x8 SSO

A próxima parte do tutorial depende do tipo de subscrição que você tem com 8x8.

* Para 8x8 Edições e clientes da Série X que utilizem o [Configure 8x8 Configuration Manager](#configure-8x8-configuration-manager).
* Para clientes do Escritório Virtual que utilizem o Gestor de Conta para administração, consulte o [Configure 8x8 Account Manager](#configure-8x8-account-manager).

### <a name="configure-8x8-configuration-manager"></a>Configure 8x8 Gestor de Configuração

1. Inicie sessão no 8x8 Diretor de [Configuração](https://vo-cm.8x8.com/).

1. A partir da página inicial clique em **Gestão de Identidade**.

    ![8x8 Gestor de Configuração](./media/8x8virtualoffice-tutorial/configure1.png)

1. Verifique o **"Single Sign On" (SSO)** e selecione **o Microsoft Azure AD**.

    ![8x8 Gestor de Configuração](./media/8x8virtualoffice-tutorial/configure2.png)

1. Copie os três URLs e o certificado de assinatura do **set Up Single Sign-On com** a página SAML no Azure AD para a secção de **Definições SAML da Microsoft Azure AD** no 8x8 Configuration Manager.

    ![8x8 Gestor de Configuração](./media/8x8virtualoffice-tutorial/configure3.png)

    a. Copy **Login URL** para **IDP Login URL**.

    b. Copy **Azure AD Identifier** para **IDP Emitente URL/URN**.

    c. Copy **Logout URL** para **IDP Logout URL**.

    d. Certificado de descarregamento **(Base64)** e upload para **Certificado**.

    e. Clique em **Guardar**.

### <a name="configure-8x8-account-manager"></a>Configure 8x8 Gestor de Conta

1. Inscreva-se no seu inquilino do Escritório Virtual 8x8 como administrador.

1. Selecione **Conta De Escritório Virtual Mgr** no Painel de Aplicação.

    ![Configurar no lado da aplicação](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

1. Selecione conta **De Negócios** para gerir e clicar no botão **'Iniciar** a' botão.

    ![Configurar no lado da aplicação](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

1. Clique no separador **CONTAS** na lista de menus.

    ![Configurar no lado da aplicação](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

1. Clique em **Iniciar sessão** individual na lista de Contas.

    ![Configurar no lado da aplicação](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

1. Selecione **sinal único sob** métodos de autenticação e clique em **SAML**.

    ![Configurar no lado da aplicação](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

1. No **Sinal Único SAML na** secção, execute os seguintes passos:

    ![Configurar no lado da aplicação](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)

    a. Na caixa de texto **STURL Sign In,** colhe o valor URL de **Login** que copiou do portal Azure.

    b. Na caixa de texto **url sign out,** cola o valor URL de **Logout** que copiou do portal Azure.

    c. Na caixa de texto **URL emitente,** pasta **Azure AD Identifier** valor que copiou do portal Azure.

    d. Clique no botão **Browse** para carregar o certificado que descarregou do portal Azure.

    e. Clique no botão **Guardar**.

### <a name="create-8x8-test-user"></a>Criar 8x8 utilizador de teste

Nesta secção, cria-se uma utilizadora chamada Britta Simon em 8x8. Trabalhe com a equipa de [suporte 8x8](https://www.8x8.com/about-us/contact-us) para adicionar os utilizadores na plataforma 8x8. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo 8x8 no Painel de Acesso, deve ser automaticamente inscrito no 8x8 para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente 8x8 com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger o 8x8 com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)