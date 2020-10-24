---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com MilEyes Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o ThousandEyes.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/04/2019
ms.author: jeedes
ms.openlocfilehash: df256749a813fe81857aa250081411afd52ae35c
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92489562"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-thousandeyes"></a>Tutorial: Azure Ative Directory integração única (SSO) com ThousandEyes

Neste tutorial, você vai aprender a integrar MilEyes com Azure Ative Direy (Azure AD). Quando integrares o ThousandEyes com a AD Azure, podes:

* Controlo em Azure AD que tem acesso a ThousandEyes.
* Permita que os seus utilizadores sejam automaticamente inscritos na ThousandEyes com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* MilEyes assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* MilEyes apoia **SP e IDP** inicia SSO
* ThousandEyes suporta fornecimento [ **automatizado** de utilizadores](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-provisioning-tutorial)

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="adding-thousandeyes-from-the-gallery"></a>Adicionando MilEyes da galeria

Para configurar a integração de MilEyes em AZure AD, você precisa adicionar MilEyes da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **da galeria,** escreva **MilEyes** na caixa de pesquisa.
1. Selecione **ThousandEyes** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-thousandeyes"></a>Configurar e testar Azure AD único sign-on para ThousandEyes

Configure e teste Azure AD SSO com MilEyes usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em ThousandEyes.

Para configurar e testar a Azure AD SSO com MilEyes, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure ThousandEyes SSO](#configure-thousandeyes-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create ThousandEyes test user](#create-thousandeyes-test-user)** - para ter uma contraparte de B.Simon in ThousandEyes que está ligada à representação AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **ThousandEyes,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** a aplicação está pré-configurada e os URLs necessários já estão pré-povoados com Azure. O utilizador precisa de guardar a configuração clicando no botão **Guardar.**

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL:  `https://app.thousandeyes.com/login/sso`

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configuração MilEyes,** copie os URL(s) apropriados com base na sua exigência.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a ThousandEyes.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **ThousandEyes**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-thousandeyes-sso"></a>Configurar milEyes SSO

1. Numa janela diferente do navegador, inscreva-se no site da empresa **MilEyes** como administrador.

2. No menu em cima, clique em **Definições**.

    ![A screenshot mostra o site ThousandEyes com Definições selecionadas.](./media/thousandeyes-tutorial/ic790066.png "Definições")

3. Clique **em Conta**

    ![A screenshot mostra a conta selecionada no menu Definições.](./media/thousandeyes-tutorial/ic790067.png "Conta")

4. Clique no separador **Autenticação &** de Segurança.

    ![Autenticação & de Segurança](./media/thousandeyes-tutorial/ic790068.png "Autenticação & de Segurança")

5. Na **secção 'Iniciar Ação Única',** execute os seguintes passos:

    ![Configurar único sinal de inscrição](./media/thousandeyes-tutorial/ic790069.png "Configuração single Sign-On")

    a. Selecione **Ative o único sinal de inscrição**.

    b. Na caixa de texto **URL do Url da página de login,** pasta **URL,** que copiou do portal Azure.

    c. Na caixa de texto **URL da página de logout,** pasta URL **logout,** que copiou do portal Azure.

    d. Caixa de texto **emitente de fornecedor** de identidade, pasta **Azure AD Identifier**, que copiou do portal Azure.

    e. No **Certificado de Verificação,** clique em **Escolher o ficheiro**e, em seguida, faça o upload do certificado que descarregou a partir do portal Azure.

    f. Clique em **Guardar**.

### <a name="create-thousandeyes-test-user"></a>Criar milieyes utilizador de teste

O objetivo desta secção é criar um utilizador chamado Britta Simon in ThousandEyes. A ThousandEyes suporta o fornecimento automático de utilizadores, que é por defeito ativado. Pode encontrar mais detalhes [aqui](thousandeyes-provisioning-tutorial.md) sobre como configurar o fornecimento automático do utilizador.

**Se precisar de criar o utilizador manualmente, execute os seguintes passos:**

1. Inscreva-se no site da sua empresa MilEyes como administrador.

2. Clique em **Definições**.

    ![A screenshot mostra o site ThousandEyes com Definições selecionadas.](./media/thousandeyes-tutorial/ic790066.png "Definições")

3. Clique **em Conta**.

    ![A screenshot mostra a conta selecionada no menu Definições.](./media/thousandeyes-tutorial/ic790067.png "Conta")

4. Clique no **separador Contas & Utilizadores.**

    ![Contas & Utilizadores](./media/thousandeyes-tutorial/IC790073.png "Contas & Utilizadores")

5. Na secção **'Adicionar utilizadores & Contas',** execute os seguintes passos:

    ![Adicionar Contas de Utilizador](./media/thousandeyes-tutorial/IC790074.png "Adicionar Contas de Utilizador")

    a. Na caixa de texto **Name,** digite o nome do utilizador como **B.Simon**.

    b. Na caixa de texto **de e-mail,** escreva o e-mail do utilizador como b.simon@contoso.com .

    b. Clique **em Adicionar Novo Utilizador à Conta.**

    > [!NOTE]
    > O titular da conta Azure Ative Directory receberá um e-mail incluindo um link para confirmar e ativar a conta.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador MilEyes ou APIs fornecidas pela ThousandEyes para fornecer contas de utilizador do Azure Ative Directory.


## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo MilEyes no Painel de Acesso, deverá ser automaticamente inscrito no ThousandEyes para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente MilEyes com Azure AD](https://aad.portal.azure.com/)

- [Configurar o Provisionamento do Utilizador](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-provisioning-tutorial)