---
title: 'Tutorial: Integração do Diretório Ativo Azure com o RFPIO Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o RFPIO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 87187076-7b50-4247-814f-f217b052703f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63d7b6af8ff76c890b98c29ded0e8bdc637b45dd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "67092854"
---
# <a name="tutorial-azure-active-directory-integration-with-rfpio"></a>Tutorial: Integração do Diretório Ativo Azure com o RFPIO

Neste tutorial, aprende-se a integrar o RFPIO com o Azure Ative Directory (Azure AD).
Integrar o RFPIO com a AD Azure proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a RFPIO.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no RFPIO (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o RFPIO, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura ativada por sinal único RFPIO

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* RFPIO suporta **SP e IDP** iniciadoS SSO

## <a name="adding-rfpio-from-the-gallery"></a>Adicionar RFPIO da galeria

Para configurar a integração do RFPIO em Azure AD, precisa adicionar RFPIO da galeria à sua lista de aplicações saaS geridas.

**Para adicionar RFPIO da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **RFPIO,** selecione **RFPIO** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

    ![RFPIO na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on azure ad com RFPIO com base num utilizador de teste chamado **Britta Simon**.
Para que o início de um único início funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no RFPIO.

Para configurar e testar o único sinal de Azure AD com o RFPIO, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sinal Único RFPIO](#configure-rfpio-single-sign-on)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie um utilizador de teste RFPIO](#create-rfpio-test-user)** - para ter uma contrapartida da Britta Simon no RFPIO que esteja ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único signo da Azure AD com o RFPIO, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **RFPIO,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** se pretender configurar a aplicação no modo iniciado **idp,** execute o seguinte passo:

    ![RfPIO Domain e URLs informações únicas de inscrição](common/idp-identifier.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://www.rfpio.com`

    b. Clique em **definir URLs adicionais**.

    c. Na caixa de texto **do Estado relé** introduza um valor de cadeia. Contacte a equipa de [suporte rfpio](https://www.rfpio.com/contact/) para obter este valor.

    ![RfPIO Domain e URLs informações únicas de inscrição](common/idp-preintegrated-relay.png)

5. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    ![image](common/both-preintegrated-signon.png)

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://www.app.rfpio.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e sign-on real. Contacte a equipa de suporte ao [cliente rfpio](https://www.rfpio.com/contact/) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

6. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Federation Metadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

7. Na secção **RFPIO configurar,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-rfpio-single-sign-on"></a>Configure o sinal único rfpio

1. Numa janela diferente do navegador web, inscreva-se no site do **RFPIO** como administrador.

1. Clique no canto inferior esquerdo.

    ![Configurar um único sinal](./media/rfpio-tutorial/app1.png)

1. Clique nas **Definições**da Organização . 

    ![Configurar um único sinal](./media/rfpio-tutorial/app2.png)

1. Clique nas **funcionalidades & INTEGRAÇÃO**.

    ![Configurar um único sinal](./media/rfpio-tutorial/app4.png)

1. Na **Edição**de cliques de **configuração SAML SSO** .

    ![Configurar um único sinal](./media/rfpio-tutorial/app3.png)

1. Nesta Secção executa as seguintes ações:

    ![Configurar um único sinal](./media/rfpio-tutorial/app5.png)
    
    a. Copie o conteúdo dos **Metadados Descarregados XML** e cole-o no campo de configuração de **identidade.**

    > [!NOTE]
    >Para copiar o conteúdo dos metadados da **Federação descarregados XML** Use O Bloco de **Notas++** ou **o editor xML**adequado .

    b. Clique em **Validar**.

    c. Depois de clicar em **Validar,** vire **o SAML (Ativado)** para ligar.

    d. Clique em **Submeter**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No **User name** tipo `brittasimon@yourcompanydomain.extension`de campo do nome do utilizador . Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso ao RFPIO.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações**e, em seguida, selecione **RFPIO**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **RFPIO**.

    ![O link RFPIO na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-rfpio-test-user"></a>Criar o utilizador de teste RFPIO

1. Inscreva-se no site da sua empresa RFPIO como administrador.

1. Clique no canto inferior esquerdo.

    ![Configurar um único sinal](./media/rfpio-tutorial/app1.png)

1. Clique nas **Definições**da Organização . 

    ![Configurar um único sinal](./media/rfpio-tutorial/app2.png)

1. Clique nos **MEMBROS da EQUIPA**.

    ![Configurar um único sinal](./media/rfpio-tutorial/app6.png)

1. Clique em **ADD MEMBERS**.

    ![Configurar um único sinal](./media/rfpio-tutorial/app7.png)

1. Na secção **Adicionar Novos Membros.** Realizar as seguintes ações:

    ![Configurar um único sinal](./media/rfpio-tutorial/app8.png)

    a. Introduza o **endereço de e-mail** no campo **de e-mail inserir um e-mail por linha.**

    b. Por favor, selecione **Role** de acordo com os seus requisitos.

    c. Clique em **ADICIONAR MEMBROS**.

    > [!NOTE]
    > O titular da conta Azure Ative Directory recebe um e-mail e segue um link para confirmar a sua conta antes de se tornar ativo.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo RFPIO no Painel de Acesso, deverá ser automaticamente inscrito no RFPIO para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

