---
title: 'Tutorial: Integração do Diretório Ativo Azure com a OpsGenie Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o OpsGenie.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 41b59b22-a61d-4fe6-ab0d-6c3991d1375f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 446ac54d84f7b2b3bf3aaf6eaf5536f0dfb804fe
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67095761"
---
# <a name="tutorial-azure-active-directory-integration-with-opsgenie"></a>Tutorial: Integração do Diretório Ativo Azure com a OpsGenie

Neste tutorial, aprende-se a integrar a OpsGenie com o Azure Ative Directory (Azure AD).
Integrar a OpsGenie com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a OpsGenie.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na OpsGenie (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a OpsGenie, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura ativada por sinal único opsGenie

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* OpsGenie apoia **SP** iniciado SSO

## <a name="adding-opsgenie-from-the-gallery"></a>Adicionando OpsGenie da galeria

Para configurar a integração da OpsGenie no Azure AD, precisa adicionar opsGenie da galeria à sua lista de aplicações geridas do SaaS.

**Para adicionar OpsGenie da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **OpsGenie**, selecione **OpsGenie** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![OpsGenie na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on azure ad com opsGenie com base num utilizador de teste chamado **B. Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em OpsGenie.

Para configurar e testar o único sinal de Azure AD com a OpsGenie, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure OpsGenie Single Sign-On](#configure-opsgenie-single-sign-on)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on azure AD com B. Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b. Simon utilize um único sinal de AD Azure.
5. **[Crie o utilizador de teste OpsGenie](#create-opsgenie-test-user)** - para ter uma contrapartida de B. Simon em OpsGenie que esteja ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único signo da Azure AD com a OpsGenie, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **OpsGenie,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![OpsGenie Domain e URLs informações únicas de inscrição](common/sp-signonurl.png)

    Na caixa de texto **de URL sign-on,** escreva um URL:`https://app.opsgenie.com/auth/login`

5. Na configuração do Single Sign-On com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique no botão de cópia para copiar o Url de **Metadados da Federação** da Aplicação e guarde-o no seu computador.

    ![O link de descarregamento do Certificado](common/copy-metadataurl.png)

6. Na secção **Configurar OpsGenie,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-opsgenie-single-sign-on"></a>Configure OpsGenie Single Sign-On

1. Abra outra instância do navegador e, em seguida, faça login na OpsGenie como administrador.

2. Clique em **Definições**e, em seguida, clique no separador **'Sinal Único'.**
   
    ![OpsGenie Single Sign-On](./media/opsgenie-tutorial/tutorial_opsgenie_06.png)

3. Para ativar o SSO, selecione **Ativado**.
   
    ![Definições opsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_07.png) 

4. Na secção **Fornecedor,** clique no separador **Diretório Ativo Azure.**
   
    ![Definições opsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_08.png) 

5. Na página de diálogo azure Ative Directory, execute os seguintes passos:
   
    ![Definições opsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_09.png)
    
    a. Na caixa de texto **SAML 2.0 Endpoint,** cola o valor URL de **Login**que copiou do portal Azure.
    
    b. No Url de **Metadados:** caixa de texto, pasta app Palavras de **metadados Valor url** que copiou do portal Azure.
    
    c. Clique em **Guardar Alterações**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado B. Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **B. Simon.**
  
    b. No tipo de campo de **nome do utilizador****bsimon@yourcompanydomain.extension**  
    Por exemplo, BSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que B. Simon utilize um único sign-on Azure, concedendo acesso à OpsGenie.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **OpsGenie**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **OpsGenie**.

    ![O link OpsGenie na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **registos de utilizadores e grupos,** selecione **B. Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-opsgenie-test-user"></a>Criar o utilizador de teste OpsGenie

O objetivo desta secção é criar um utilizador chamado B. Simon em OpsGenie. 

1. Numa janela do navegador web, inicie sessão no seu inquilino OpsGenie como administrador.

2. Navegue na lista de Utilizadores clicando em **Utilizadores** no painel esquerdo.
   
    ![Definições opsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_10.png) 

3. Clique em **Adicionar Utilizador**.

4. No diálogo **Adicionar utilizador,** execute os seguintes passos:
   
    ![Definições opsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_11.png)
   
    a. Na caixa de texto **por e-mail,** digite o endereço de e-mail de B. Simon endereçado no Diretório Ativo Azure.
   
    b. Na caixa de texto **Nome Completo,** tipo **B. Simon**.
   
    c. Clique em **Guardar**. 

>[!NOTE]
>B. O Simon recebe um e-mail com instruções para configurar o perfil.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo OpsGenie no Painel de Acesso, deve ser automaticamente inscrito no OpsGenie para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

