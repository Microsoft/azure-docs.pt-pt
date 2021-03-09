---
title: 'Tutorial: Integração do Diretório Ativo Azure com Jitbit Helpdesk | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Jitbit Helpdesk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/02/2021
ms.author: jeedes
ms.openlocfilehash: 26d9321f028bc8b9293e9d7d46616fff2f3b60ce
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102486875"
---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Tutorial: Integração do Diretório Ativo Azure com Jitbit Helpdesk

Neste tutorial, você vai aprender a integrar Jitbit Helpdesk com Azure Ative Directory (Azure AD). Quando integrar o Jitbit Helpdesk com Azure AD, pode:

* Controle em Azure AD que tem acesso a Jitbit Helpdesk.
* Ative os seus utilizadores a serem automaticamente inscritos no Jitbit Helpdesk com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Jitbit Helpdesk assinatura única ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Jitbit Helpdesk suporta SSO iniciado **SP.**

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="add-jitbit-helpdesk-from-the-gallery"></a>Adicione Jitbit Helpdesk da galeria

Para configurar a integração do Jitbit Helpdesk no AD Azure, precisa adicionar Jitbit Helpdesk da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **da galeria,** digite **Jitbit Helpdesk** na caixa de pesquisa.
1. Selecione **Jitbit Helpdesk** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-jitbit-helpdesk"></a>Configure e teste Azure AD SSO para Jitbit Helpdesk

Configure e teste Azure AD SSO com Jitbit Helpdesk usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Jitbit Helpdesk.

Para configurar e testar o Azure AD SSO com Jitbit Helpdesk, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Igigure Jitbit Helpdesk SSO](#configure-jitbit-helpdesk-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Jitbit Helpdesk test user](#create-jitbit-helpdesk-test-user)** - para ter uma contraparte de B.Simon em Jitbit Helpdesk que está ligada à representação AD Ad Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Jitbit Helpdesk,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    a. Na caixa de texto **URL,** digite um dos URLs utilizando o seguinte padrão:
    | |
    | ----------------------------------------|
    | `https://<hostname>/helpdesk/User/Login`|
    | `https://<tenant-name>.Jitbit.com`|
    | |
    
    > [!NOTE] 
    > Este valor não é real. Atualize este valor com o URL Sign-On real. Contacte [a equipa de suporte do cliente Jitbit Helpdesk](https://www.jitbit.com/support/) para obter este valor.

    b. Na caixa de texto **identifier (Entity ID),** digite o URL: `https://www.jitbit.com/web-helpdesk/`

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Configurar Jitbit Helpdesk,** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Jitbit Helpdesk.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Jitbit Helpdesk**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-jitbit-helpdesk-sso"></a>Configure Jitbit Helpdesk SSO

1. Numa janela diferente do navegador web, inscreva-se no site da empresa Jitbit Helpdesk como administrador.

1. Na barra de ferramentas em cima, clique em **Administração**.

    ![Administration](./media/jitbit-helpdesk-tutorial/settings.png "Administração")

1. Clique **nas definições gerais**.

    ![A screenshot mostra a ligação Configurações Gerais.](./media/jitbit-helpdesk-tutorial/general.png "Utilizadores, empresas e permissões")

1. Na secção de configuração de **configuração de definições de autenticação,** execute os seguintes passos:

    ![Definições de autenticação](./media/jitbit-helpdesk-tutorial/authentication.png "Definições de autenticação")

    a. **Selecione Ativar o sinal único SAML 2.0**, para iniciar sinsução utilizando Sign-On único (SSO), com **OneLogin**.

    b. Na caixa de texto **url do EndPoint,** cole o valor do URL de **login** que copiou do portal Azure.

    c. Abra o certificado codificado **base-64** no bloco de notas, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o na caixa de texto **do Certificado X.509**

    d. Clique **em Guardar as alterações**.

### <a name="create-jitbit-helpdesk-test-user"></a>Criar utilizador de teste Jitbit Helpdesk

Para permitir que os utilizadores de Azure AD inscrevam-se no Jitbit Helpdesk, devem ser a provisionados no Jitbit Helpdesk. No caso do Jitbit Helpdesk, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no seu inquilino **Jitbit Helpdesk.**

1. No menu em cima, clique em **Administração.**

    ![Administration](./media/jitbit-helpdesk-tutorial/settings.png "Administração")

1. Clique em **Utilizadores, empresas e permissões.**

    ![Utilizadores, empresas e permissões](./media/jitbit-helpdesk-tutorial/users.png "Utilizadores, empresas e permissões")

1. Clique **em Adicionar utilizador**.

    ![Adicionar utilizador](./media/jitbit-helpdesk-tutorial/add.png "Adicionar utilizador")

1. Na secção Criar, digite os dados da conta Azure AD que pretende obter da seguinte forma:

    ![Criar](./media/jitbit-helpdesk-tutorial/create-section.png "Criar")

   a. Na caixa de texto **username,** digite o nome de utilizador do utilizador como **BrittaSimon**.

   b. Na caixa de texto **por e-mail,** escreva e-mail do utilizador como **BrittaSimon@contoso.com** .

   c. Na caixa de texto **Name Name,** escreva o primeiro nome do utilizador como **Britta**.

   d. Na caixa de texto **Do Último Nome,** escreva o apelido do utilizador como **Simon**.

   e. Clique em **Criar**.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador Jitbit Helpdesk ou APIs fornecidas pela Jitbit Helpdesk para fornecer contas de utilizador Azure AD.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de sinal de sinal de Jitbit, onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de sinal de ajuda jitbit e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo Jitbit Helpdesk nas Minhas Apps, este será redirecionado para o URL de sinal de ajuda jitbit. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Jitbit Helpdesk, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
