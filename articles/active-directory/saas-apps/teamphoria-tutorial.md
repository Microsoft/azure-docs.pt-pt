---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com teamphoria [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Teamphoria.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d569c705-6f0f-4ec1-b485-ba82526b5d32
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/09/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2631b34f5658c9d4f76ca26d378bc63fe59ad156
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "72373255"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-teamphoria"></a>Tutorial: Azure Ative Directory integração individual (SSO) com teamphoria

Neste tutorial, aprenderá a integrar teamphoria com o Azure Ative Directory (Azure AD). Quando integrar a Teamphoria com o Azure AD, pode:

* Controlo em Azure AD que tem acesso à Teamphoria.
* Permita que os seus utilizadores sejam automaticamente inscritos na Teamphoria com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Teamphoria single sign-on (SSO) enabled subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Teamphoria apoia **SP** iniciado SSO

## <a name="adding-teamphoria-from-the-gallery"></a>Adicionar Teamphoria da galeria

Para configurar a integração da Teamphoria em Azure AD, você precisa adicionar Teamphoria da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, digite **Teamphoria** na caixa de pesquisa.
1. Selecione **Teamphoria** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-teamphoria"></a>Configure e teste Azure AD único sign-on para Teamphoria

Configure e teste Azure AD SSO com Teamphoria utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Teamphoria.

Para configurar e testar o Azure AD SSO com a Teamphoria, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure teamphoria SSO](#configure-teamphoria-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Crie o utilizador do teste Teamphoria](#create-teamphoria-test-user)** - para ter uma contraparte de B.Simon em Teamphoria que esteja ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **teamphoria,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<sub-domain>.teamphoria.com/login`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de Sign-On real. Contacte a equipa de apoio ao [Cliente da Teamphoria](https://www.teamphoria.com/) para obter o valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção De Configuração da **Teamphoria,** copie os URL(s) adequados com base na sua exigência.

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

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso à Teamphoria.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Teamphoria**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-teamphoria-sso"></a>Configure Teamphoria SSO

1. Para automatizar a configuração dentro da Teamphoria, precisa de instalar a extensão de **navegador Secure-in das Minhas Aplicações** clicando em **instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Configurar teamphoria** irá direcioná-lo para a aplicação Teamphoria. A partir daí, forneça as credenciais de administração para assinar na Teamphoria. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-6.

    ![Configuração de configuração de configuração](common/setup-sso.png)

3. Se quiser configurar a Teamphoria manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa Teamphoria como administrador e execute os seguintes passos:

4. Vá à opção **DEDEFINIÇÕES DE ADMINISTRAÇÃO** na barra de ferramentas esquerda e sob o separador Configurar clique no **SIGN-ON SINGLE** para abrir a janela de configuração SSO.

    ![Configurar um único sinal](./media/teamphoria-tutorial/admin_sso_configure.png)

5. Clique na opção **ADD NEW IDENTITY PROVIDER** no canto superior direito para abrir o formulário para adicionar as definições para SSO.

    ![Configurar um único sinal](./media/teamphoria-tutorial/add_new_identity_provider.png)

6. Introduza os detalhes nos campos descritos abaixo...

    ![Configurar um único sinal](./media/teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **NOME DO VISUALIZAÇÃO**: Introduza o nome do visor do plugin na página de administração.

    b. **NOME DO BOTÃO**: O nome do separador que irá visualizar na página de login para iniciar sessão através do SSO.

    c. **CERTIFICADO**: Abra o Certificado descarregado anteriormente do portal Azure no bloco de notas, copie o conteúdo do mesmo e cole-o aqui na caixa.

    d. **PONTO DE ENTRADA**: Colar o URL de **login** copiado anteriormente do portal Azure.

    e. Mude a opção para **ON** e clique em **SAVE**.

### <a name="create-teamphoria-test-user"></a>Criar o utilizador do teste teamphoria

Para permitir que os utilizadores da AD Azure inscrevam-se na Teamphoria, devem ser aprovisionados na Teamphoria. No caso da Teamphoria, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no site da sua empresa teamphoria como administrador.

1. Clique nas definições **ADMIN** na barra de ferramentas esquerda e no separador **MANAGE** Clique nos **UTILIZADORES** para abrir a página de administração para os utilizadores.

    ![Adicionar Empregado](./media/teamphoria-tutorial/admin_manage_users.png)

1. Clique na opção **CONVITE MANUAL.**

    ![Convidar pessoas](./media/teamphoria-tutorial/admin_manage_add_users.png)

1. Nesta página, execute a seguinte ação.

    ![Convidar pessoas](./media/teamphoria-tutorial/manual_user_invite.png)

    a. Na caixa de texto **e-mail,** introduza o endereço de **e-mail** do utilizador como B.Simon.

    b. Na caixa de texto **FIRST NAME,** introduza o primeiro nome do utilizador como **B**.

    c. Na caixa de texto **LAST NAME,** introduza o último nome do utilizador como **Simon**.

    d. Clique em **Convidar 1 UTILIZADOR**. O utilizador precisa de aceitar o convite para ser criado no sistema.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Teamphoria no Painel de Acesso, deve ser automaticamente inscrito na Teamphoria para a qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente teamphoria com Azure AD](https://aad.portal.azure.com/)

