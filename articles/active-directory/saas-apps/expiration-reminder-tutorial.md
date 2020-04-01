---
title: 'Tutorial: Integração do Diretório Ativo Azure com Aviso de Expiração [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Diretório Ativo azure e o lembrete de expiração.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 3df3d73e-80a4-4b35-bd3d-90bc4f6f6afd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8261ab34398d8961faf0abda8df17db1be3bfdd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73158182"
---
# <a name="tutorial-azure-active-directory-integration-with-expiration-reminder"></a>Tutorial: Integração do Diretório Ativo Azure com Lembrete de Expiração

Neste tutorial, aprende-se a integrar o Lembrete de Expiração com o Diretório Ativo Azure (Azure AD).
Integrar o Lembrete de Expiração com a AD Azure proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso a Aviso de Validade.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Aviso de Validade (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Lembrete de Expiração, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura ativada por sinal único do Lembrete de Expiração

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Aviso de expiração suporta **SP** iniciado SSO

## <a name="adding-expiration-reminder-from-the-gallery"></a>Adicionar lembrete de expiração da galeria

Para configurar a integração do Lembrete de Expiração em Anúncio De Azure, precisa de adicionar O Lembrete de Expiração da galeria à sua lista de aplicações geridas do SaaS.

**Para adicionar Aviso de Validade da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva Lembrete de **Expiração,** selecione Lembrete de **Validade** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![Lembrete de validade na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sinal de Azure AD com Aviso de Expiração baseado num utilizador de teste chamado **Britta Simon**.
Para que o início de sessão simples funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Lembrete de Expiração.

Para configurar e testar o único sinal de Azure AD com O Lembrete de Expiração, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o lembrete de expiração single sign-on](#configure-expiration-reminder-single-sign-on)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie o utilizador](#create-expiration-reminder-test-user)** do teste de lembrete de expiração - para ter uma contrapartida de Britta Simon em Lembrete de Expiração que esteja ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sinal de Azure AD com O Lembrete de Validade, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações de lembrete de **validade,** selecione **single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Domínio de lembrete de validade e informações únicas de inscrição](common/sp-signonurl.png)

    Na caixa de texto **de URL sign-on,** escreva um URL:`https://app.expirationreminder.net/account/sso`

5. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Raw)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificateraw.png)

6. Na secção 'Lembrete de **validade' configurar,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de anúncio sinuoso

    c. Logout URL

### <a name="configure-expiration-reminder-single-sign-on"></a>Configure expiração lembrete único sign-on

Para configurar um único sinal no lado do Lembrete de **Expiração,** você precisa enviar o Certificado descarregado **(Raw)** e URLs copiados apropriados do portal Azure para a equipa de suporte de Lembrete de [Expiração](mailto:support@expirationreminder.net). Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

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

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso ao Lembrete de Expiração.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione 'Lembrete de **Validade'.**

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, escreva e selecione Lembrete de **Validade**.

    ![O link de lembrete de expiração na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-expiration-reminder-test-user"></a>Criar utilizador de teste de lembrete de expiração

Nesta secção, cria-se uma utilizadora chamada Britta Simon em "Expiration Reminder". Trabalhe com a equipa de suporte do Lembrete de [Expiração](mailto:support@expirationreminder.net) para adicionar os utilizadores na plataforma 'Lembrete de Expiração'. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo 'Lembrete de Validade' no Painel de Acesso, deverá ser automaticamente inscrito no Lembrete de Expiração para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
