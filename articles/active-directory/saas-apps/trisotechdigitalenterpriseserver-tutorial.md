---
title: 'Tutorial: Integração de Diretório Ativo Azure com o Trisotech Digital Enterprise Server [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Trisotech Digital Enterprise Server.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6d54d20c-eca1-4fa6-b56a-4c3ed0593db0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 56399f99ede611c4a120603cce3a3eede2728c6d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "67088252"
---
# <a name="tutorial-azure-active-directory-integration-with-trisotech-digital-enterprise-server"></a>Tutorial: Integração de Diretório Ativo Azure com o Trisotech Digital Enterprise Server

Neste tutorial, aprende-se a integrar o Trisotech Digital Enterprise Server com o Azure Ative Directory (Azure AD).
Integrar o Trisotech Digital Enterprise Server com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao Trisotech Digital Enterprise Server.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Trisotech Digital Enterprise Server (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Trisotech Digital Enterprise Server, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Trisotech Digital Enterprise Server única subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Trisotech Digital Enterprise Server suporta **SP** iniciado SSO

* Trisotech Digital Enterprise Server suporta o fornecimento de utilizadores **justo no tempo**

## <a name="adding-trisotech-digital-enterprise-server-from-the-gallery"></a>Adicionar o Servidor Digital de Empresa seletiva Trisotech da galeria

Para configurar a integração do Trisotech Digital Enterprise Server em Azure AD, precisa adicionar o Trisotech Digital Enterprise Server da galeria à sua lista de aplicações geridas saaS.

**Para adicionar o Trisotech Digital Enterprise Server da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite o **Trisotech Digital Enterprise Server,** selecione **Trisotech Digital Enterprise Server** do painel de resultados e, em seguida, clique em adicionar o botão **Adicionar** a aplicação.

     ![Trisotech Digital Enterprise Server na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sign-on azure ad com o Trisotech Digital Enterprise Server baseado num utilizador de teste chamado **Britta Simon**.
Para um único início de sessão funcionar, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Trisotech Digital Enterprise Server.

Para configurar e testar o único sign-on da Azure AD com o Trisotech Digital Enterprise Server, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Single Sign-On do Servidor De Empresa Digital Trisotech](#configure-trisotech-digital-enterprise-server-single-sign-on)** - para configurar as definições de entrada única no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. Crie o utilizador de **[teste Trisotech Digital Enterprise Server](#create-trisotech-digital-enterprise-server-test-user)** - para ter uma contrapartida da Britta Simon no Trisotech Digital Enterprise Server que está ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on da Azure AD com o Trisotech Digital Enterprise Server, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações do **Servidor Digital De Empresa Seletiva Trisotech,** selecione Single **sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Trisotech Digital Enterprise Server Domain e URLs informações únicas de inscrição](common/sp-identifier.png)

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<companyname>.trisotech.com`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`https://<companyname>.trisotech.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e identificador. Contacte a equipa de suporte do Cliente do [Cliente do Cliente da Trisotech Digital Enterprise Server](mailto:support@trisotech.com) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

4. Na configuração do Single Sign-On com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique no botão de cópia para copiar o Url de **Metadados da Federação** da Aplicação e guarde-o no seu computador.

    ![O link de descarregamento do Certificado](common/copy-metadataurl.png)

### <a name="configure-trisotech-digital-enterprise-server-single-sign-on"></a>Configure Trisotech Digital Enterprise Server Single Sign-On

1. Numa janela de navegador web diferente, inscreva-se no site da empresa de configuração de servidores digitais da Empresa Trisotech como administrador.

2. Clique no **ícone Menu** e, em seguida, selecione **Administration**.

    ![Configurar um único sinal](./media/trisotechdigitalenterpriseserver-tutorial/user1.png)

3. Selecione **Fornecedor de Utilizador**.

    ![Configurar um único sinal](./media/trisotechdigitalenterpriseserver-tutorial/user2.png)

4. Na secção configurações do fornecedor de utilizadores, execute os **seguintes** passos:

    ![Configurar um único sinal](./media/trisotechdigitalenterpriseserver-tutorial/user3.png)

    a. Selecione **Secured Assertion Markup Language 2 (SAML 2)** a partir da queda no método de **autenticação**.

    b. Na caixa de texto URL de **Metadados,** colá o valor url de **metadados da Federação de Aplicações,** que copiou forma o portal Azure.

    c. Na caixa de texto id da **aplicação,** introduza o URL utilizando o seguinte padrão: `https://<companyname>.trisotech.com`.

    d. Clique em **Guardar**

    e. Introduza o nome de domínio na caixa de texto **De domínios permitidos (vazio significa todos)** e atribui automaticamente licenças para utilizadores que correspondam aos Domínios Permitidos

    f. Clique em **Guardar**

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No **User name** tipo brittasimon@yourcompanydomain.extensionde campo do nome do utilizador . Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que a Britta Simon utilize um único sign-on Azure, concedendo acesso ao Trisotech Digital Enterprise Server.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **Trisotech Digital Enterprise Server**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Trisotech Digital Enterprise Server**.

    ![O link Trisotech Digital Enterprise Server na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-trisotech-digital-enterprise-server-test-user"></a>Criar o utilizador de teste do Servidor Digital Enterprise Da Trisotech

Nesta secção, um utilizador chamado Britta Simon é criado no Trisotech Digital Enterprise Server. O Trisotech Digital Enterprise Server suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador ainda não existir no Trisotech Digital Enterprise Server, um novo é criado após a autenticação.

>[!Note]
>Se precisar de criar um utilizador manualmente, contacte a equipa de suporte do [Trisotech Digital Enterprise Server](mailto:support@trisotech.com).

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Trisotech Digital Enterprise Server no Painel de Acesso, deve ser automaticamente inscrito no Servidor Digital de Empresa seletiva Trisotech para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

