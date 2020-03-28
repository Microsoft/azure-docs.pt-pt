---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com a AcquireIO [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o AcquireIO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 97815e13-32ec-4470-b075-1253f5814f4f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01bcc3678485119afae1d567d97eff9dcebe6b95
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "76714617"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-acquireio"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com a AcquireIO

Neste tutorial, aprenderá a integrar a AcquireIO com o Azure Ative Directory (Azure AD). Quando integrar a AcquireIO com a Azure AD, pode:

* Controlo em Azure AD que tem acesso à AcquireIO.
* Ative que os seus utilizadores sejam automaticamente inscritos na AcquireIO com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Adquirir uma única subscrição (SSO) ativada pela acquireio.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* AcquireIO suporta **IDP** iniciado SSO

## <a name="adding-acquireio-from-the-gallery"></a>Adicionar AcquireIO da galeria

Para configurar a integração da AcquireIO em Azure AD, precisa de adicionar a AcquireIO da galeria à sua lista de aplicações geridas do SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **AcquireIO** na caixa de pesquisa.
1. **Selecione AcquireIO** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-acquireio"></a>Configure e teste Azure AD único sign-on para AcquireIO

Configure e teste Azure AD SSO com AcquireIO utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na AcquireIO.

Para configurar e testar o Azure AD SSO com a AcquireIO, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure AcquireIO SSO](#configure-acquireio-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Create AcquireIO test user](#create-acquireio-test-user)** - para ter uma contrapartida de B.Simon em AcquireIO que está ligada à representação azure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **AcquireIO,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://app.acquire.io/ad/<acquire_account_uid>`

    > [!NOTE]
    > O valor não é real. Você receberá o URL de resposta real que é explicado mais tarde na secção **Configure AcquireIO** do tutorial. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção **Configuração de AcquireIO,** copie os URL(s) adequados com base no seu requisito.

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

Nesta secção, permitirá que a B.Simon utilize um único sign-on azure, concedendo acesso à AcquireIO.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **AcquireIO**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-acquireio-sso"></a>Configure AcquireIO SSO

1. Para automatizar a configuração dentro do AcquireIO, é necessário instalar a extensão de **'Sign-in' de Aplicações Seguras,** clicando em **instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

1. Depois de adicionar a extensão ao navegador, clique em **Configurar AcquireIO,** que o direciona para a aplicação AcquireIO. A partir daí, forneça as credenciais de administração para iniciar sessão na AcquireIO. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-6.

    ![Configuração de configuração de configuração](common/setup-sso.png)

1. Se pretender configurar o AcquireIO manualmente, numa janela de navegador web diferente, inscreva-se na AcquireIO como Administrador.

1. Do lado esquerdo do menu, clique na **App Store**.

     ![Configuração AcquireIO](./media/acquireio-tutorial/config01.png)

1. Desloque-se até ao **Diretório Ativo** e clique em **Instalar**.

    ![Configuração AcquireIO](./media/acquireio-tutorial/config02.png)

1. No pop-up Ative Directory, execute os seguintes passos:

    ![Configuração AcquireIO](./media/acquireio-tutorial/config03.png)

    a. Clique em **Copiar** para copiar o URL de resposta para a sua instância e cole-o na caixa de texto URL de **resposta** na secção **de configuração Básica SAML** no portal Azure.

    b. Na caixa de texto **login URL,** cola o valor do URL de **Login,** que copiou do portal Azure.

    c. Abra o certificado codificado Base64 no Bloco de Notas, copie o seu conteúdo e cole-o na caixa de texto **x.509.**

    d. Clique em **Ligar agora**.

### <a name="create-acquireio-test-user"></a>Criar utilizador de teste AcquireIO

Para permitir que os utilizadores da Azure AD entrem na AcquireIO, devem ser aprovisionados na AcquireIO. Na AcquireIO, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Numa janela de navegador web diferente, inscreva-se na AcquireIO como Administrador.

1. Do lado esquerdo do menu, clique em **Perfis** e navegue para **Adicionar Perfil**.

     ![Configuração AcquireIO](./media/acquireio-tutorial/config04.png)

1. No pop-up do **cliente Add,** execute os seguintes passos:

    ![Configuração AcquireIO](./media/acquireio-tutorial/config05.png)

    a. Em **Nome** da caixa de texto, introduza o nome de utilizador como **B.simon**.

    b. Na caixa de texto **por e-mail,** introduza o e-mail do utilizador como **B.simon@contoso.com**.

    c. Clique em **Submeter**.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo AcquireIO no Painel de Acesso, deverá ser automaticamente inscrito no AcquireIO para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente adquirir io com a Azure AD](https://aad.portal.azure.com/)
