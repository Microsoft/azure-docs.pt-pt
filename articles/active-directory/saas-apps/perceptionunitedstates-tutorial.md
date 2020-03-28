---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Perception United States (Non-UltiPro) [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Perception United States (Non-UltiPro).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b4a8f026-cb5f-41eb-9680-68eddc33565e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: e9ba42f780c93486409077383750d0635637e99b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67094842"
---
# <a name="tutorial-azure-active-directory-integration-with-perception-united-states-non-ultipro"></a>Tutorial: Integração do Diretório Ativo Azure com a Perception United States (Non-UltiPro)

Neste tutorial, aprende-se a integrar a Perception United States (Non-UltiPro) com o Azure Ative Directory (Azure AD).
Integrar a Perceção Dos Estados Unidos (Não UltiPro) com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Perception United States (Non-UltiPro).
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Perception United States (Non-UltiPro) (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a Perception United States (Non-UltiPro), precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Perceção Estados Unidos (Não UltiPro) assinatura única ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Perception United States (Non-UltiPro) suporta **IDP** iniciado SSO

## <a name="adding-perception-united-states-non-ultipro-from-the-gallery"></a>Adicionar Perception United States (Não-UltiPro) da galeria

Para configurar a integração da Perception United States (Non-UltiPro) no Azure AD, é necessário adicionar a Perception United States (Non-UltiPro) da galeria à sua lista de aplicações geridas pela SaaS.

**Para adicionar Perception United States (Non-UltiPro) da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Perception United States (Non-UltiPro)**, selecione **Perception United States (Non-UltiPro)** do painel de resultados e, em seguida, clique em **Adicionar** botão para adicionar a aplicação.

     ![Perception United States (Non-UltiPro) na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on azure ad com perception Estados Unidos (Não-UltiPro) com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na Perception United States (Non-UltiPro).

Para configurar e testar o único signo da Azure AD com a Perception United States (Non-UltiPro), é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure perception United States (Non-UltiPro) Single Sign-On](#configure-perception-united-states-non-ultipro-single-sign-on)** - para configurar as definições de início de sessão única no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Create Perception United States (Non-UltiPro) test user](#create-perception-united-states-non-ultipro-test-user)** - to have a counterpart of Britta Simon in Perception United States (Non-UltiPro) que está ligada à representação da AD Azure do utilizador.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único signo da AD Azure com a Perception United States (Non-UltiPro), execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações da **Perception United States (Non-UltiPro),** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na configuração de um único sign-on com a página **SAML,** execute os seguintes passos:

    ![Perceção Estados Unidos (Não UltiPro) Domínio e URLs informações únicas de inscrição](common/idp-intiated.png)

    a. Na caixa de texto **identificador,** digite um URL:`https://perception.kanjoya.com/sp`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://perception.kanjoya.com/sso?idp=<entity_id>`

    c. A aplicação **Perception United States (Non-UltiPro)** requer o valor do **Identificador AD Azure** como <entity_id>, que obterá da secção **set up Perception United States (Non-UltiPro),** para ser codificado. Para obter o valor codificado uri, **http://www.url-encode-decode.com/** utilize o seguinte link: .

    d. Depois de obter o valor codificado uri combine-o com o **URL de resposta** como mencionado abaixo-

    `https://perception.kanjoya.com/sso?idp=<URI encooded entity_id>`
    
    e. Colar o valor acima na caixa de texto **URL resposta.**

5. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Federation Metadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

6. Na secção **Configurar Perception United States (Não UltiPro),** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL   

### <a name="configure-perception-united-states-non-ultipro-single-sign-on"></a>Configure Perception United States (Non-UltiPro) Single Sign-On

1. Noutra janela do navegador, inscreva-se no site da sua empresa Perception United States (Non-UltiPro) como administrador.

2. Na barra de ferramentas principal, clique em **Definições de Conta**.

    ![Utilizador dos Estados Unidos da Perceção (Não-UltiPro)](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_user.png)

3. Na página Definições da **Conta,** execute os seguintes passos:

    ![Utilizador dos Estados Unidos da Perceção (Não-UltiPro)](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_account.png)

    a. Na caixa de texto Nome da **Empresa,** digite o nome da **Empresa**.
    
    b. Na caixa de texto Nome da **Conta,** digite o nome da **Conta**.

    c. Na caixa de texto **'Resposta-E-mail',** escreva o **email**válido .

    d. Selecione **SSO Identity Provider** como **SAML 2.0**.

4. Na página de **Configuração SSO,** execute os seguintes passos:

    ![Perceção Estados Unidos (Não-UltiPro) SSOConfig](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_ssoconfig.png)

    a. Selecione **O Tipo de Identificação de Nome SAML** como **e-mail**.

    b. Na caixa de texto **SSO Nome de Configuração,** escreva o nome da sua **Configuração**.
    
    c. Na caixa de texto Nome do Fornecedor de **Identidade,** cola o valor do **Identificador Azure AD,** que copiou do portal Azure. 

    d. Na caixa de **texto sAML Domain,** introduza o domínio como @contoso.com.

    e. Clique em **Upload Novamente** para carregar o ficheiro **Metadata XML.**

    f. Clique em **Atualizar**.

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

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso à Perception United States (Non-UltiPro).

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **Perception United States (Non-UltiPro)**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Perception United States (Non-UltiPro)**.

    ![O link Perception United States (Non-UltiPro) na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-perception-united-states-non-ultipro-test-user"></a>Criar utilizadores de teste da Perception United States (Non-UltiPro)

Nesta secção, cria-se uma utilizadora chamada Britta Simon in Perception United States (Non-UltiPro). Trabalhar com a equipa de suporte da [Perception United States (Non-UltiPro)](https://www.ultimatesoftware.com/Contact/ContactUs) para adicionar os utilizadores na plataforma Perception United States (Non-UltiPro).

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Perception United States (Non-UltiPro) no Painel de Acesso, deve ser automaticamente inscrito na Perception United States (Non-UltiPro) para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

