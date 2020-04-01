---
title: 'Tutorial: Integração de Diretório Sonícola Azure com RunMyProcess [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Diretório Ativo Azure e o RunMyProcess.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d31f7395-048b-4a61-9505-5acf9fc68d9b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46c31a209e8521b24e7f604dbe630f689fca484e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "68880383"
---
# <a name="tutorial-integrate-runmyprocess-with-azure-active-directory"></a>Tutorial: Integrar runMyProcess com diretório ativo azure

Neste tutorial, você vai aprender a integrar runMyProcess com O Diretório Ativo Azure (Azure AD). Quando integrar o RunMyProcess com o Azure AD, pode:

* Controlo em Azure AD que tem acesso ao RunMyProcess.
* Ative que os seus utilizadores sejam automaticamente inscritos no RunMyProcess com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* ExecutarMyProcess única subscrição ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* RunMyProcess suporta **SP** iniciado SSO

## <a name="adding-runmyprocess-from-the-gallery"></a>Adicionar RunMyProcess da galeria

Para configurar a integração do RunMyProcess em AD Azure, precisa de adicionar o RunMyProcess da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **RunMyProcess** na caixa de pesquisa.
1. Selecione **RunMyProcess** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Configure e teste Azure AD SSO com RunMyProcess utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no RunMyProcess.

Para configurar e testar o Azure AD SSO com o RunMyProcess, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure RunMyProcess SSO](#configure-runmyprocess-sso)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
5. **[Create RunMyProcess test user](#create-runmyprocess-test-user)** - para ter uma contraparte de B.Simon no RunMyProcess que está ligada à representação do utilizador da AD Azure.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **RunMyProcess,** encontre a secção **Gerir** e selecione **um único sinal .**
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://live.runmyprocess.com/live/<tenant id>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de Sign-On real. Contacte a equipa de suporte ao [Cliente RunMyProcess](mailto:support@runmyprocess.com) para obter o valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Na configuração de um único sinal com página **SAML,** na secção certificado de **assinatura SAML,** encontre **certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção **'ExecutarMyProcess',** copie os URL(s) adequados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-runmyprocess-sso"></a>Configure RunMyProcess SSO

1. Numa janela de navegador web diferente, inscreva-se no seu inquilino RunMyProcess como administrador.

1. No painel de navegação esquerdo, clique em **Conta** e selecione **Configuração**.

    ![Configure um único sinal no lado da aplicação](./media/runmyprocess-tutorial/tutorial_runmyprocess_001.png)

1. Vá à secção do método de **autenticação** e execute abaixo os passos:

    ![Configure um único sinal no lado da aplicação](./media/runmyprocess-tutorial/tutorial_runmyprocess_002.png)

    a. Como **Método,** selecione **SSO com Samlv2**.

    b. Na caixa de texto **redirecionada SSO,** cola o valor do URL de **Login,** que copiou do portal Azure.

    c. Na caixa de texto **de redirecionamento de Logout,** colhe o valor do URL de **Logout**, que copiou do portal Azure.

    d. Na caixa de texto **formato nome ID,** digite o valor do **formato identificador** de nome como **urn:oasis:nomes:tc:SAML:1.1:nameid-format:emailAddress**.

    e. Abra o ficheiro de certificado descarregado do portal Azure no bloco de notas, copie o conteúdo do ficheiro de certificado e, em seguida, cole-o na caixa de texto **do Certificado.**

    f. Clique no ícone **Guardar.**

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

Nesta secção, permitirá que a B.Simon utilize um único sign-on azure, concedendo acesso ao RunMyProcess.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **RunMyProcess**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

### <a name="create-runmyprocess-test-user"></a>Criar o utilizador de teste RunMyProcess

Para permitir que os utilizadores de Anúncios Azure assinem o RunMyProcess, devem ser aprovisionados no RunMyProcess. No caso do RunMyProcess, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no site da empresa RunMyProcess como administrador.

1. Clique em **Conta** e selecione **Utilizadores** no painel de navegação esquerdo e, em seguida, clique em **Novo Utilizador**.

    ![Novo Utilizador](./media/runmyprocess-tutorial/tutorial_runmyprocess_003.png "Novo Utilizador")

1. Na secção Definições do **Utilizador,** execute os seguintes passos:

    ![Perfil](./media/runmyprocess-tutorial/tutorial_runmyprocess_004.png "Perfil")
  
    a. Digite o **Nome** e **e-mail** de uma conta Azure AD válida que pretende fornecer nas caixas de texto relacionadas.

    b. Selecione uma **linguagem IDE,** **Linguagem**e **Perfil.**

    c. Selecione **Enviar-me e-mail de criação de conta**para mim .

    d. Clique em **Guardar**.

    > [!NOTE]
    > Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador RunMyProcess ou APIs fornecidas pelo RunMyProcess para fornecer contas de utilizador do Diretório Ativo Azure.

### <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo RunMyProcess no Painel de Acesso, deverá ser automaticamente inscrito no RunMyProcess para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)