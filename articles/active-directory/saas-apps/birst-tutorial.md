---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Birst Agile Business Analytics [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Birst Agile Business Analytics.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 677183b1-5348-4302-88cc-5c8ab63a3c6c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46bea4afcd59a385a8e9d2ccf1aa0d70fdae198c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73158846"
---
# <a name="tutorial-azure-active-directory-integration-with-birst-agile-business-analytics"></a>Tutorial: Integração de Diretório Ativo Azure com birst Agile Business Analytics

Neste tutorial, aprende-se a integrar o Birst Agile Business Analytics com o Azure Ative Directory (Azure AD).
Integrar a Birst Agile Business Analytics com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Birst Agile Business Analytics.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Birst Agile Business Analytics (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a Birst Agile Business Analytics, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura de assinatura única ativada de assinatura seletiva de Negócios Birst Agile

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Birst Agile Business Analytics apoia **SP** iniciado SSO

## <a name="adding-birst-agile-business-analytics-from-the-gallery"></a>Adicionar Birst Agile Business Analytics da galeria

Para configurar a integração do Birst Agile Business Analytics no Azure AD, você precisa adicionar Birst Agile Business Analytics da galeria à sua lista de aplicações geridas saaS.

**Para adicionar Birst Agile Business Analytics da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Birst Agile Business Analytics,** selecione **Birst Agile Business Analytics** do painel de resultados e, em seguida, clique em **Adicionar** botão para adicionar a aplicação.

     ![Birst Agile Business Analytics na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o single sign-on azure ad com birst Agile Business Analytics com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Birst Agile Business Analytics.

Para configurar e testar o único sign-on da Azure AD com a Birst Agile Business Analytics, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Single Sign-On do Birst Agile Business Analytics](#configure-birst-agile-business-analytics-single-sign-on)** - para configurar as definições de entrada única no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. Crie o utilizador de **[testes Birst Agile Business Analytics](#create-birst-agile-business-analytics-test-user)** - para ter uma contrapartida da Britta Simon em Birst Agile Business Analytics que está ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on da Azure AD com o Birst Agile Business Analytics, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **birst Agile Business Analytics,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Birst Agile Business Analytics Domain e URLs informações únicas de inscrição](common/sp-intiated.png)

    Na caixa de texto **"Sign-on URL",** escreva um URL utilizando o seguinte padrão:`https://login.bws.birst.com/SAMLSSO/Services.aspx?birst.idpid=TENANTIDPID`

    O URL depende do datacenter que a sua conta Birst está localizada:

   * Para a utilização do datacenter dos EUA seguindo o padrão:`https://login.bws.birst.com/SAMLSSO/Services.aspx?birst.idpid=TENANTIDPID`

   * Para o datacenter europeu, utilize o seguinte padrão:`https://login.eu1.birst.com/SAMLSSO/Services.aspx?birst.idpid=TENANTIDPID`

     > [!NOTE]
     > Este valor não é real. Atualize o valor com o URL de Sign-On real. Contacte a equipa de apoio ao [cliente da Birst Agile Business Analytics](mailto:info@birst.com) para obter o valor.

5. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

6. Na secção **Configurar Agile Business Analytics,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de anúncio sinuoso

    c. Logout URL

### <a name="configure-birst-agile-business-analytics-single-sign-on"></a>Configure Birst Agile Business Analytics Single Sign-On

Para configurar um único sign-on no lado **birst Agile Business Analytics,** você precisa enviar o Certificado descarregado **(Base64)** e URLs copiados apropriados do portal Azure para [birst Agile Business Analytics equipe](mailto:info@birst.com)de suporte . Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

> [!NOTE]
> Mencione à equipa birst que esta integração precisa de algoritmo SHA256 (SHA1 não será suportado) para que possam definir o SSO no servidor apropriado, como **app2101** etc.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No **tipo** de campo de nome utilizador **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso ao Birst Agile Business Analytics.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **Birst Agile Business Analytics**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Birst Agile Business Analytics**.

    ![O link Birst Agile Business Analytics na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-birst-agile-business-analytics-test-user"></a>Criar o utilizador de teste Birst Agile Business Analytics

Nesta secção, cria-se uma utilizadora chamada Britta Simon em Birst Agile Business Analytics. Trabalhe com a equipa de [suporte birst Agile Business Analytics](mailto:info@birst.com) para adicionar os utilizadores na plataforma Birst Agile Business Analytics. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Birst Agile Business Analytics no Painel de Acesso, deve ser automaticamente inscrito no Birst Agile Business Analytics para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

