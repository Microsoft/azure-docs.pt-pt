---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com workteam | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Workteam.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/19/2019
ms.author: jeedes
ms.openlocfilehash: 077af1e8add63aac9f3d866c5d665ad4a47b133f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92520052"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workteam"></a>Tutorial: Azure Ative Directory integração única (SSO) com workteam

Neste tutorial, você vai aprender a integrar workteam com Azure Ative Directory (Azure AD). Quando integrar a Workteam com a Azure AD, pode:

* Controlo em Azure AD que tem acesso à Workteam.
* Capacitar os seus utilizadores a serem automaticamente inscritos na Workteam com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição ativada por workteam single sign-on (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Workteam apoia **SP e IDP** iniciado SSO

## <a name="adding-workteam-from-the-gallery"></a>Adicionar Workteam da galeria

Para configurar a integração da Workteam no Azure AD, é necessário adicionar workteam da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **escreva workteam** na caixa de pesquisa.
1. Selecione **Workteam** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-workteam"></a>Configurar e testar Azure AD único sign-on para workteam

Configure e teste Azure AD SSO com workteam usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na Workteam.

Para configurar e testar a Azure AD SSO com workteam, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure workteam SSO](#configure-workteam-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Workteam test user](#create-workteam-test-user)** - para ter uma contraparte de B.Simon in Workteam que está ligada à representação AZure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **workteam,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** a aplicação está pré-configurada no modo iniciado pelo **IDP** e os URLs necessários já estão pré-povoados com Azure. O utilizador precisa de guardar a configuração clicando no botão **Guardar.**

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL:  `https://app.workte.am`

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar a equipa de trabalho, copie** os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à workteam.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Workteam**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="configure-workteam-sso"></a>Configurar workteam SSO

1. Para automatizar a configuração dentro da Workteam, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Setup Workteam** irá direcioná-lo para a aplicação Workteam. A partir daí, forneça as credenciais de administração para assinar na Workteam. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-6.

    ![Configuração de configuração](common/setup-sso.png)

3. Se pretender configurar o Workteam manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa Workteam como administrador e execute os seguintes passos:

4. No canto superior direito clique no **logotipo do perfil** e, em seguida, clique nas **definições da Organização**. 

    ![Definições de workteam](./media/workteam-tutorial/tutorial_workteam_settings.png)

5. Na secção **autenticação,** clique no **logótipo Definições**.

     ![Workteam azure](./media/workteam-tutorial/tutorial_workteam_azure.png)

6. Na página **definições DE SAML,** execute os seguintes passos:

     ![Saml workteam](./media/workteam-tutorial/tutorial_workteam_saml.png)

    a. Selecione **SAML IdP** como **AD Azure**.

    b. Na caixa de texto URL de **serviço Sign-On único SAML,** cole o valor do URL de **login,** que copiou a partir do portal Azure.

    c. Na caixa de texto **ID da Entidade SAML,** cole o valor do **Identificador AD Azure,** que copiou a partir do portal Azure.

    d. No Bloco de Notas, abra o **certificado codificado base-64** que descarregou a partir do portal Azure, copie o seu conteúdo e, em seguida, cole-o na caixa do **Certificado de Assinatura SAML (Base64).**

    e. Clique em **OK**.

### <a name="create-workteam-test-user"></a>Criar utilizador de teste workteam

Para permitir que os utilizadores da Azure AD inscrevam-se na Workteam, devem ser a provisionados na Workteam. Na workteam, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se na Workteam como Administrador de Segurança.

2. No meio superior da página de **definições** da Organização, clique em **UTILIZADORES** e, em seguida, clique em **NOVO UTILIZADOR**.

    ![Utilizador da workteam](./media/workteam-tutorial/tutorial_workteam_user.png)

3. Na página do **Novo Empregado,** execute os seguintes passos:

    ![Workteam novo utilizador](./media/workteam-tutorial/tutorial_workteam_newuser.png)

    a. Na caixa de texto **Name,** insira o primeiro nome do utilizador como **B.Simon**.

    b. Na caixa de texto **por e-mail,** insira o e-mail do utilizador como `B.Simon\@contoso.com` .

    c. Clique em **OK**.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo workteam no Painel de Acesso, deverá ser automaticamente inscrito na Workteam para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente workteam com Azure AD](https://aad.portal.azure.com/)