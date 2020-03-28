---
title: 'Tutorial: Integração do Diretório Ativo Azure com coupa Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Coupa.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 47f27746-9057-4b9c-991e-3abf77710f73
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 709f3a5b66db660ade482660cd9b3930ff0b7141
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74227656"
---
# <a name="tutorial-azure-active-directory-integration-with-coupa"></a>Tutorial: Integração do Diretório Ativo Azure com coupa

Neste tutorial, aprende-se a integrar coupa com o Azure Ative Directory (Azure AD).
Integrar coupa com AD Azure proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso a Coupa.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Coupa (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com coupa, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura de um único sinal de Coupa

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Coupa apoia **SP** iniciado SSO

## <a name="adding-coupa-from-the-gallery"></a>Adicionando Coupa da galeria

Para configurar a integração de Coupa em Azure AD, você precisa adicionar Coupa da galeria à sua lista de aplicações saaS geridas.

**Para adicionar Coupa da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Coupa**, selecione **Coupa** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![Coupa na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sinal de Azure AD com Coupa com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Coupa.

Para configurar e testar o único sinal de Azure AD com coupa, você precisa completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure coupa single sign-on](#configure-coupa-single-sign-on)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie um utilizador](#create-coupa-test-user)** de teste coupa - para ter uma contrapartida de Britta Simon em Coupa que esteja ligada à representação da AD Azure do utilizador.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único signo da Azure AD com coupa, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **coupa,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Coupa Domain e URLs informações únicas de inscrição](common/sp-identifier-reply.png)

    a. Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<companyname>.coupahost.com`

    > [!NOTE]
    > O valor do URL de inscrição não é real. Atualize este valor com o URL de Início de Sinal real. Contacte a equipa de suporte do [Cliente Coupa](https://success.coupa.com/Support/Contact_Us?) para obter este valor.

    b. Na caixa **de identificador,** digite um URL:

    | Ambiente  | do IdP |
    |:-------------|----|
    | Sandbox | `sso-stg1.coupahost.com`|
    | Produção | `sso-prd1.coupahost.com`|
    | | |

    c. Na caixa de texto **URL resposta,** escreva um URL:

    | Ambiente | do IdP |
    |------------- |----|
    | Sandbox | `https://sso-stg1.coupahost.com/sp/ACS.saml2`|
    | Produção | `https://sso-prd1.coupahost.com/sp/ACS.saml2`|
    | | |

4. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Federation Metadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

6. Na secção **Configurar Coupa,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de anúncio sinuoso

    c. Logout URL

### <a name="configure-coupa-single-sign-on"></a>Configure Coupa Single Sign-On

1. Inscreva-se no site da empresa coupa como administrador.

2. Vá ao Controlo de Segurança de **Configuração \> **.

    ![Controlos de Segurança](./media/coupa-tutorial/ic791900.png "Controlos de Segurança")

3. No Log in utilizando a secção **de credenciais Coupa,** execute os seguintes passos:

    ![Metadados Coupa SP](./media/coupa-tutorial/ic791901.png "Metadados Coupa SP")

    a. Selecione **Iniciar sessão utilizando o SAML**.

    b. Clique em **Navegar** para fazer o upload dos metadados descarregados do portal Azure.

    c. Clique em **Guardar**.

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

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso a Coupa.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações**e, em seguida, selecione **Coupa**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Coupa**.

    ![O link Coupa na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-coupa-test-user"></a>Criar o utilizador de teste coupa

Para permitir que os utilizadores da AD Azure entrem em Coupa, devem ser aprovisionados em Coupa.  

* No caso de Coupa, o provisionamento é uma tarefa manual.

**Para configurar o fornecimento do utilizador, execute os seguintes passos:**

1. Inicie sessão no site da empresa **coupa** como administrador.

2. No menu em cima, clique em **Configurar**, e, em seguida, clique em **Utilizadores**.

    ![Utilizadores](./media/coupa-tutorial/ic791908.png "Utilizadores")

3. Clique em **Criar**.

    ![Criar Utilizadores](./media/coupa-tutorial/ic791909.png "Criar Utilizadores")

4. Na secção Criação de **Utilizadores,** execute os seguintes passos:

    ![Detalhes do utilizador](./media/coupa-tutorial/ic791910.png "Detalhes do utilizador")

    a. Digite o **Login**, **Primeiro Nome,** **Apelido,** ID de Início único de **Inscrição,** Atributos de **e-mail** de uma conta de Diretório Ativo Azure válida que pretende fornecer nas caixas de texto relacionadas.

    b. Clique em **Criar**.

    >[!NOTE]
    >O titular da conta Azure Ative Directory receberá um e-mail com um link para confirmar a conta antes de se tornar ativa.
    >

>[!NOTE]
>Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador coupa ou APIs fornecidas pela Coupa para fornecer contas de utilizador Da Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Coupa no Painel de Acesso, deve ser automaticamente inscrito no Coupa para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

