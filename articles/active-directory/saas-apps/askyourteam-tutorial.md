---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com askYourTeam [ Integração de um único sign-on do Azure Ative Diretório) com a AskYourTeam [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o AskYourTeam.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1cef6764-de54-4920-b0ad-e560c214c72e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/28/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 403f39de1b7d226d9feeb33388c32f63271fdcd6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "78968547"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-askyourteam"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com askYourTeam

Neste tutorial, aprenderá a integrar o AskYourTeam com o Azure Ative Directory (Azure AD). Quando integrar o AskYourTeam com o Azure AD, pode:

* Controle em Azure AD que tem acesso a AskYourTeam.
* Ative que os seus utilizadores sejam automaticamente inscritos no AskYourTeam com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Pergunte a subscrição ativada por um único sinal (SSO) do AskYourTeam.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* AskYourTeam suporta **SP e IDP** iniciado SSO.
* Assim que configurar o AskYourTeam, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

## <a name="adding-askyourteam-from-the-gallery"></a>Adicionar AskYourTeam da galeria

Para configurar a integração do AskYourTeam em Azure AD, precisa de adicionar askYourTeam da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** **escreva AskYourTeam** na caixa de pesquisa.
1. Selecione **AskYourTeam** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-askyourteam"></a>Configure e teste Azure AD único sign-on para AskYourTeam

Configure e teste Azure AD SSO com AskYourTeam utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no AskYourTeam.

Para configurar e testar o Azure AD SSO com askYourTeam, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure AskYourTeam SSO](#configure-askyourteam-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Create AskYourTeam test user](#create-askyourteam-test-user)** - para ter uma contraparte de B.Simon no AskYourTeam que está ligado à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **AskYourTeam,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** caso deseje configurar a aplicação no modo iniciado do **IDP,** introduza os valores para os seguintes campos:

    Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<COMPANY>.app.askyourteam.com/users/auth/saml/callback`

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<COMPANY>.app.askyourteam.com/login`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com os valores reais de URL de Resposta e URL de Sinal que são explicados posteriormente no tutorial.

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção **'AskYourTeam',** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, permitirá que a B.Simon utilize um único sign-on azure, concedendo acesso ao AskYourTeam.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **AskYourTeam**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-askyourteam-sso"></a>Configure AskYourTeam SSO

1. Numa janela diferente do navegador web, inscreva-se no site da AskYourTeam como administrador.

1. Clique na **Minha Organização.**

    ![Pergunte a configuração do seu team](./media/askyourteam-tutorial/user1.png)

1. Clique em **Integrações**.

    ![Pergunte a configuração do seu team](./media/askyourteam-tutorial/configure1.png)

1. Clique em **Definições de Edição**.

    ![Pergunte a configuração do seu team](./media/askyourteam-tutorial/configure2.png)

1. Na página de **Integração de Sinais Únicos de Edição,** execute os seguintes passos: 

    ![Pergunte a configuração do seu team](./media/askyourteam-tutorial/configure3.png)

    a. Na caixa de texto URL do Serviço De **Sinalização Única SAML,** colá o valor URL de **Login** que copiou do portal Azure.

    b. Na caixa de texto Id da **Entidade SAML,** colá o valor do **Identificador Azure AD** que copiou do portal Azure.

    c. Na caixa de texto **URL sign-out,** colá o valor URL de **Logout** que copiou do portal Azure.

    d. Abra o Certificado descarregado **(Base64)** do portal Azure para o Notepad e colhe o conteúdo no Certificado de **Assinatura SAML - Base64** textbox.

    > [!NOTE]
    > Em alternativa, também pode carregar o ficheiro **Federation Metadata XML** clicando na opção **Escolha Ficheiro.**

    e. Valor do URL de Resposta a Cópia (URL do **Serviço de Consumidor de Afirmação),** cole este valor na caixa de texto URL de **resposta** na secção **de configuração Básica SAML** no portal Azure.

    f. Copy Sign no valor **URL,** cole este valor no **Signo na** caixa de texto URL na secção **de configuração SAML básica** no portal Azure.

    g. Clique em **Guardar**.

### <a name="create-askyourteam-test-user"></a>Criar o utilizador de teste AskYourTeam

1. Numa janela diferente do navegador web, inscreva-se no site da AskYourTeam como administrador.

1. Clique na **Minha Organização.**

    ![Pergunte a configuração do seu team](./media/askyourteam-tutorial/user1.png)

1. Clique em **Utilizadores** e selecione **Novo Utilizador**.

    ![Pergunte a configuração do seu team](./media/askyourteam-tutorial/user2.png)

1. Na secção **Novo utilizador,** execute os seguintes passos:

    ![Pergunte a configuração do seu team](./media/askyourteam-tutorial/user3.png)

    1. Na caixa de texto **de primeiro nome,** introduza o primeiro nome do utilizador.

    1. Na caixa de texto **De apelido,** introduza o último nome do utilizador.

    1. Na caixa de texto **e-mail,** introduza o endereço de e-mail do utilizador como B.Simon@contoso.com.

    1. Selecione a **Função** para o utilizador de acordo com o requisito da sua organização.

    1. Clique em **Guardar**.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo AskYourTeam no Painel de Acesso, deve ser automaticamente inscrito no AskYourTeam para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente askYourTeam com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
