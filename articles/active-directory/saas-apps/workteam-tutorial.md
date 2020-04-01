---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com workteam [ Integração de diretório sonuoso com workteam ] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Workteam.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 41df17a1-ba69-414f-8ec3-11079b030df6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b14c08604fcc0f6d2550127ca1f1aa053172b75
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "72026746"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workteam"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com workteam

Neste tutorial, você vai aprender a integrar o Workteam com o Azure Ative Directory (Azure AD). Quando integrar o Workteam com a Azure AD, pode:

* Controlo em Azure AD que tem acesso a Workteam.
* Ative que os seus utilizadores sejam automaticamente inscritos no Workteam com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A subscrição ativada por um único sinal de equipa de trabalho (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Workteam suporta **SP e IDP** iniciadoS SSO

## <a name="adding-workteam-from-the-gallery"></a>Adicionar workteam da galeria

Para configurar a integração do Workteam em Azure AD, precisa de adicionar o Workteam da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **workteam** na caixa de pesquisa.
1. Selecione **workteam** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-workteam"></a>Configure e teste Azure AD único sign-on para workteam

Configure e teste Azure AD SSO com workteam utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Workteam.

Para configurar e testar o Azure AD SSO com workteam, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure o Workteam SSO](#configure-workteam-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Create Workteam test user](#create-workteam-test-user)** - para ter uma contraparte de B.Simon no Workteam que esteja ligada à representação azure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **workteam,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica saml,** a aplicação está pré-configurada no modo iniciado **idp** e os URLs necessários já estão pré-povoados com o Azure. O utilizador precisa de guardar a configuração clicando no botão **Guardar.**

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL:`https://app.workte.am`

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção Configurar o **Workteam,** copie os URL(s) adequados com base na sua exigência.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

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

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso ao Workteam.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Workteam**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

### <a name="configure-workteam-sso"></a>Configure Workteam SSO

1. Para automatizar a configuração dentro do Workteam, precisa de instalar a extensão de **navegador Secure-in das Minhas Aplicações** clicando em **instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique no **Workteam configurar-lhe-á** para a aplicação Workteam. A partir daí, forneça as credenciais de administração para assinar no Workteam. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-6.

    ![Configuração de configuração de configuração](common/setup-sso.png)

3. Se pretender configurar o Workteam manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa Workteam como administrador e execute os seguintes passos:

4. No canto superior direito clique no logotipo do **perfil** e, em seguida, clique nas **definições da Organização**. 

    ![Definições de team-team](./media/workteam-tutorial/tutorial_workteam_settings.png)

5. Na secção **AUTENTICAÇÃO,** clique no **logótipo Definições**.

     ![Workteam azure](./media/workteam-tutorial/tutorial_workteam_azure.png)

6. Na página **Definições SAML,** execute os seguintes passos:

     ![Saml de vapor de trabalho](./media/workteam-tutorial/tutorial_workteam_saml.png)

    a. Selecione **SAML IDP** como **AD Azure**.

    b. Na caixa de texto URL do Serviço De **Sinalização Única SAML,** colá o valor do URL de **Login,** que copiou do portal Azure.

    c. Na caixa de texto Id da **Entidade SAML,** colhe o valor do **Identificador Azure AD,** que copiou do portal Azure.

    d. No Notepad, abra o **certificado codificado base-64** que descarregou do portal Azure, copie o seu conteúdo e, em seguida, cole-o na caixa **de assinatura SAML (Base64).**

    e. Clique em **OK**.

### <a name="create-workteam-test-user"></a>Criar o utilizador de teste workteam

Para permitir que os utilizadores de Anúncios Azure entrem no Workteam, devem ser aprovisionados no Workteam. No Workteam, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no Workteam como Administrador de Segurança.

2. No meio superior da página de **definições** da Organização, clique em **UTILIZADORES** e, em seguida, clique em **NOVO UTILIZADOR**.

    ![Utilizador de teamteam de trabalho](./media/workteam-tutorial/tutorial_workteam_user.png)

3. Na página **do novo empregado,** execute os seguintes passos:

    ![Novo utilizador do workteam](./media/workteam-tutorial/tutorial_workteam_newuser.png)

    a. Na caixa de texto **Name,** introduza o primeiro nome de utilizador como **B.Simon**.

    b. Na caixa de texto **por e-mail,** introduza o e-mail do utilizador como `B.Simon\@contoso.com`.

    c. Clique em **OK**.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo workteam no Painel de Acesso, deve ser automaticamente inscrito no Workteam para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Workteam com a Azure AD](https://aad.portal.azure.com/)

