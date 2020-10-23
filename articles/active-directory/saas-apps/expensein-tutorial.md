---
title: 'Tutorial: Integração do Azure Ative Directory com DespesasIn ; Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o ExpenseIn.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/17/2020
ms.author: jeedes
ms.openlocfilehash: eb7062b745ae6eb97dc2cd4ba2051cc221031630
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92453778"
---
# <a name="tutorial-integrate-expensein-with-azure-active-directory"></a>Tutorial: Integrar DespesasIn com Diretório Ativo Azure

Neste tutorial, você aprenderá a integrar o ExpenseIn com o Azure Ative Directory (Azure AD). Quando integra o ExpenseIn com a AD Azure, pode:

* Controlo em Azure AD que tem acesso a ExpenseIn.
* Capacitar os seus utilizadores a serem automaticamente inscritos no ExpenseIn com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Despesas Em uma única subscrição ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste. 
* DespesasIn suporta **SP e IDP** iniciado SSO.
* Uma vez configurar despesas Pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).


## <a name="adding-expensein-from-the-gallery"></a>Adicionar DespesasIn da galeria

Para configurar a integração de ExpenseIn em Azure AD, é necessário adicionar DespesasIn da galeria à sua lista de aplicações geridas do SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **escreva DespesasNa** caixa de pesquisa.
1. Selecione **DespesasIn** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-expensein"></a>Configure e teste Azure AD SSO para despesasIn

Configure e teste Azure AD SSO com ExpenseIn usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em ExpenseIn.

Para configurar e testar a Azure AD SSO com ExpenseIn, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user)** para testar o Azure AD com B.Simon.
    1. **[Atribua ao utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que a B.Simon utilize um único sinal de Ad AD.
1. **[Configure despesas No SSO](#configure-expensein-sso)** para configurar as definições SSO no lado da aplicação.
    1. **[Crie o utilizador de teste Desemudo](#create-expensein-test-user)** para ter uma contraparte de B.Simon em ExpenseIn que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **ExpenseIn,** encontre a secção **Gerir** e selecione **'Único sinal de s-on'.**
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** o utilizador não tem de realizar quaisquer passos, uma vez que a aplicação já está pré-integrada com o Azure.

5. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL:  `https://app.expensein.com/saml`

1. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique no botão de cópia para copiar o **Url de metadados da Federação de Aplicações** e clique em **Descarregar** para descarregar o **Certificado (Base64)** e guardá-lo no seu computador.

   ![O link de descarregamento de certificado](./media/expensein-tutorial/copy-metdataurl-certificate.png)

1. Na secção **Configurar DespesasIn,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a ExpenseIn.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **ExpenseIn**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**


## <a name="configure-expensein-sso"></a>Configure despesas Em SSO

1. Para automatizar a configuração dentro do ExpenseIn, é necessário instalar a extensão do **navegador 'As aplicações' Secure's,** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

1. Depois de adicionar extensão ao navegador, clique em **Configurar DespesasIn** irá direcioná-lo para a aplicação ExpenseIn. A partir daí, forneça as credenciais de administração para assinar em ExpenseIn. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-5.

    ![Configuração de configuração](common/setup-sso.png)

1. Se pretender configurar as despesas manualmente, inicie sessão no seu site da empresa Desemudo como administrador.

1. Clique em **Administração** no topo da página e, em seguida, navegue para **O Único Sign-On** e clique em **Adicionar fornecedor**.

     ![Screenshot que mostra o separador "Administrador" e a página "Single Sign-On - Fornecedores" e "Add Provider" selecionados.](./media/expenseIn-tutorial/config01.png)

1. No pop-up do Novo Fornecedor de **Identidade,** execute os seguintes passos:

    ![Screenshot que mostra o pop-up "Edit Identity Provider" com valores introduzidos.](./media/expenseIn-tutorial/config02.png)

    a. Na caixa de texto **'Nome provedor',** digite o nome; por exemplo, Azure.

    b. Selecione **Sim** para Permitir a **inscrição no fornecedor**.

    c. In the **Target Url** text box, paste the value of **Login URL**, which you have copied from Azure portal.

    d. Na caixa de texto **emitente,** cole o valor do **Identificador AD AZure,** que copiou do portal Azure.

    e. Abra o Certificado (Base64) no Bloco de Notas, copie o seu conteúdo e cole-o na caixa de texto **do Certificado.**

    f. Clique em **Criar**.

### <a name="create-expensein-test-user"></a>Criar DespesasIn utilizador de teste

Para permitir que os utilizadores de Azure AD inscrevam-se no ExpenseIn, devem ser a provisionados em ExpenseIn. Em DespesasIn, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se na ExpenditureIn como Administrador.

2. Clique em **Administração** no topo da página e navegue para **os Utilizadores** e clique em **Novo Utilizador**.

     ![Screenshot que mostra o separador "Administrador" e a página "Gerir os Utilizadores" com "Novo Utilizador" selecionado.](./media/expenseIn-tutorial/config03.png)

3. No pop-up **Details,** execute os seguintes passos:

    ![DespesasIn configuração](./media/expenseIn-tutorial/config04.png)

    a. Na caixa de texto **First Name,** insira o primeiro nome do utilizador como **B**.

    b. Na caixa de texto **Last Name,** insira o último nome do utilizador como **Simon**.

    c. Na caixa de texto **por e-mail,** insira o e-mail do utilizador como `B.Simon@contoso.com` .

    d. Clique em **Criar**.

## <a name="test-sso"></a>Teste SSO

Quando selecionar o azulejo Desemin no Painel de Acesso, deverá ser automaticamente inscrito na ExpenditureIn para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)

- [Experimente DespesasIn com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger as despesasIn com visibilidade e controlos avançados](/cloud-app-security/proxy-intro-aad)