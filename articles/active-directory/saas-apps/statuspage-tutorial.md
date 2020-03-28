---
title: 'Tutorial: Integração do Diretório Ativo Azure com statusPage [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Diretório Ativo azure e o StatusPage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f6ee8bb3-df43-4c0d-bf84-89f18deac4b9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: d947f610e6a753ce2ed349917640b07a55bbb735
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67089882"
---
# <a name="tutorial-azure-active-directory-integration-with-statuspage"></a>Tutorial: Integração de Diretório Ativo Azure com StatusPage

Neste tutorial, aprende-se a integrar o StatusPage com o Azure Ative Directory (Azure AD).
Integrar o StatusPage com a AD Azure proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso ao StatusPage.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no StatusPage (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o StatusPage, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Subscrição ativada por sinal único da Página de StatusPage

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* StatusPage suporta **IDP** iniciado SSO

## <a name="adding-statuspage-from-the-gallery"></a>Adicionar StatusPage da galeria

Para configurar a integração do StatusPage em Azure AD, precisa de adicionar statusPage da galeria à sua lista de aplicações saaS geridas.

**Para adicionar statusPage da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite o **StatusPage**, selecione **StatusPage** do painel de resultados e, em seguida, clique em adicionar o botão **Adicionar** a aplicação.

    ![Página de Estado na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sign-on azure com statusPage com base num utilizador de teste chamado **Britta Simon**.
Para que o início de sessão simples funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no StatusPage.

Para configurar e testar o único sinal de Azure AD com o StatusPage, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configurar o 'Sign-On' single da página](#configure-statuspage-single-sign-on)** de estado - para configurar as definições de início de sessão individual no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. Criar o utilizador de **[teste statusPage](#create-statuspage-test-user)** - para ter uma contraparte de Britta Simon no StatusPage que esteja ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure ad com statusPage, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **da StatusPage,** selecione **um único sinal .**

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na configuração de um único sign-on com a página **SAML,** execute os seguintes passos:

    ![StatusPage Domain e URLs informações únicas de inscrição](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:
    
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/`|
    | `https://<subdomain>.statuspage.io/`|

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:
    
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/sso/saml/consume`|
    | `https://<subdomain>.statuspage.io/sso/saml/consume`|

    > [!NOTE]
    > Contacte a equipa [SupportTeam@statuspage.io](mailto:SupportTeam@statuspage.io)de suporte do StatusPage para solicitar metadados necessários para configurar um único sinal. 
    >
    > a. A partir dos metadados, copie o valor emitente e, em seguida, cole-o na caixa de texto **do Identificador.**
    >
    > b. A partir dos metadados, copie o URL de resposta e, em seguida, cole-o na caixa de texto **URL resposta.**

5. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

6. Na secção Configurar a Página de **Estado,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-statuspage-single-sign-on"></a>Configurar o signo único da página de status

1. Noutra janela do navegador, inscreva-se no site da sua empresa statusPage como administrador.

1. Na barra de ferramentas principal, clique em **Gerir a Conta**.

    ![Configurar um único sinal](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Clique no separador **De Assinatura Única.**

    ![Configurar um único sinal](./media/statuspage-tutorial/tutorial_statuspage_07.png)

1. Na página de Configuração SSO, execute os seguintes passos:

    ![Configurar um único sinal](./media/statuspage-tutorial/tutorial_statuspage_08.png)

    ![Configurar um único sinal](./media/statuspage-tutorial/tutorial_statuspage_09.png)

    a. Na caixa de texto **SSO Target URL,** colá o valor do URL de **Login,** que copiou do portal Azure.

    b. Abra o certificado descarregado no Bloco de Notas, copie o conteúdo e, em seguida, cole-o na caixa de texto **do Certificado.**

    c. Clique na **CONFIGURAÇÃO SAVE**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No tipo de campo de **nome do utilizador**`brittasimon@yourcompanydomain.extension`  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso ao StatusPage.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações**e, em seguida, selecione **StatusPage**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **StatusPage**.

    ![O link StatusPage na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-statuspage-test-user"></a>Criar o utilizador de teste StatusPage

O objetivo desta secção é criar um utilizador chamado Britta Simon no StatusPage.

StatusPage suporta o provisionamento just-in-time. Já o ativou no [Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on).

**Para criar um utilizador chamado Britta Simon no StatusPage, execute os seguintes passos:**

1. Inscreva-se no site da sua empresa statusPage como administrador.

1. No menu em cima, clique em **Gerir a Conta**.

    ![Configurar um único sinal](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Clique no separador **Membros da Equipa.**
  
    ![Criação de um utilizador de teste azure AD](./media/statuspage-tutorial/tutorial_statuspage_10.png) 

1. Clique em **adicionar membro da equipa**.
  
    ![Criação de um utilizador de teste azure AD](./media/statuspage-tutorial/tutorial_statuspage_11.png) 

1. Digite o **Endereço de E-mail,** **Primeiro Nome**e **Sobrenome** de um utilizador válido que pretende fornecer nas caixas de texto relacionadas. 

    ![Criação de um utilizador de teste azure AD](./media/statuspage-tutorial/tutorial_statuspage_12.png) 

1. Como **Função,** escolha **Administrador de Cliente.**

1. Clique em **CRIAR CONTA**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo 'StatusPage' no Painel de Acesso, deve ser automaticamente inscrito na Página de Status para a qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
