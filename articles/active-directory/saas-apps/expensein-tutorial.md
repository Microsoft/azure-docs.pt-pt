---
title: 'Tutorial: Integração do Diretório Ativo Azure com DespesasIn [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Diretório Ativo azure e o ExpenseIn.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 6ac8053b-a216-45d8-bf5e-ecd37d808e57
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c09542013dff3a18965d1070216a938c26a144e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "67102846"
---
# <a name="tutorial-integrate-expensein-with-azure-active-directory"></a>Tutorial: Integrar Despesas Com Diretório Ativo Azure

Neste tutorial, você vai aprender a integrar O ExpenseIn com o Azure Ative Directory (Azure AD). Quando integrar o ExpenseIn com a Azure AD, pode:

* Controlo em Azure AD que tem acesso a ExpenseIn.
* Ative que os seus utilizadores sejam automaticamente inscritos no ExpenseIn com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* DespesasIn uma subscrição de inscrição única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste. DespesasIn suporta **SP e IDP** iniciado SSO.

## <a name="adding-expensein-from-the-gallery"></a>Adicionar DespesasNa da galeria

Para configurar a integração de ExpenseIn em Azure AD, precisa adicionar ExpenseIn da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, digite **Despesas Na** caixa de pesquisa.
1. Selecione **ExpenseIn** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Configure e teste Azure AD SSO com ExpenseIn utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em ExpenseIn.

Para configurar e testar o Azure AD SSO com O Em despesas, complete os seguintes blocos de construção:

1. **[Configure O SSO AD Azure](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure as despesas Para](#configure-expensein)** configurar as definições SSO no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** para testar o único sinal de Azure AD com B.Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que b.Simon utilize um único sinal de AD Azure.
5. Criar O utilizador do **[teste ExpenseIn](#create-expensein-test-user)** para ter uma contrapartida de B.Simon em ExpenseIn que esteja ligada à representação da AD Azure do utilizador.
6. **[Teste sSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **ExpenseIn,** encontre a secção **Gerir** e selecione **single sign-on**.
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** se pretender configurar a aplicação no modo iniciado **idp,** execute o seguinte passo:

    Na caixa de texto **URL resposta,** digite qualquer um dos URL:

    | |
    |--|
    | `https://app.expensein.com/samlcallback` |
    | `https://mobileapi.expensein.com/identity/samlcallback` |

5. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL:`https://app.expensein.com/saml`

1. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique no botão de cópia para copiar o Url de **Metadados da Federação da Aplicação** e clique em **Descarregar** para descarregar o **Certificado (Base64)** e guardá-lo no seu computador.

   ![O link de descarregamento do Certificado](./media/expensein-tutorial/copy-metdataurl-certificate.png)

1. Na secção **Configurar DespesasIn,** copie os URL(s) adequados com base no seu requisito.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-expensein"></a>Configure despesasDentro

1. Para automatizar a configuração dentro do ExpenseIn, precisa de instalar a extensão de **navegador Secure-in das Minhas Aplicações** clicando em **instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em Despesas de **ConfiguraçãoIrá** direcioná-lo para a aplicação ExpenseIn. A partir daí, forneça as credenciais de administração para assinar em ExpenseIn. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-5.

    ![Configuração de configuração de configuração](common/setup-sso.png)

3. Se pretender configurar o ExpenseIn manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa ExpenseIn como administrador e execute os seguintes passos:

4. Clique em **Admin** na parte superior da página e depois navegue para **Single Sign-On** e clique em **Adicionar fornecedor**.

     ![Configuração de Despesas](./media/expenseIn-tutorial/config01.png)

5. No **pop-up do Novo Fornecedor** de Identidade, execute os seguintes passos:

    ![Configuração de Despesas](./media/expenseIn-tutorial/config02.png)

    a. Na caixa de texto **'Nome** fornecedora', digite o nome ex:Azure.

    b. Selecione **Sim** como permitir o início de **sessão intitada do fornecedor**.

    c. Na caixa de texto **Target Url,** cola o valor do URL de **Login,** que copiou do portal Azure.

    d. Na caixa de texto **emitente,** cola o valor do **Identificador AD Azure,** que copiou do portal Azure.

    e. Abra o Certificado (Base64) no Bloco de Notas, copie o seu conteúdo e cole-o na caixa de texto **do Certificado.**

    f. Clique em **Criar**.

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

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso ao ExpenseIn.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **ExpenseIn**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

### <a name="create-expensein-test-user"></a>Criar DespesasNo utilizador de teste

Para permitir que os utilizadores de Anúncios Azure assinem ao ExpenseIn, devem ser aprovisionados no ExpenseIn. No ExpenseIn, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no ExpenseIn como Administrador.

2. Clique em **Admin** na parte superior da página e depois navegue para **utilizadores** e clique em **Novo Utilizador**.

     ![Configuração de Despesas](./media/expenseIn-tutorial/config03.png)

3. No pop-up **Details,** execute os seguintes passos:

    ![Configuração de Despesas](./media/expenseIn-tutorial/config04.png)

    a. Na caixa de texto **First Name,** introduza o primeiro nome do utilizador como **B**.

    b. Na caixa de texto **De Apelido,** introduza o último nome de utilizador como **Simon**.

    c. Na caixa de texto **por e-mail,** introduza o e-mail do utilizador como `B.Simon@contoso.com`.

    d. Clique em **Criar**.

### <a name="test-sso"></a>Teste SSO

Quando selecionar o azulejo ExpenseIn no Painel de Acesso, deve ser automaticamente inscrito nas Despesas Para as quais configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
