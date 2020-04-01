---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Nimblex Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Nimblex.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d3e165a5-f062-4b50-ac0b-b400838e99cd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: jeedes
ms.openlocfilehash: cd7199e94a58b3f0c121a0fd9401bff94406ed84
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67096105"
---
# <a name="tutorial-azure-active-directory-integration-with-nimblex"></a>Tutorial: Integração do Diretório Ativo Azure com a Nimblex

Neste tutorial, aprende-se a integrar o Nimblex com o Azure Ative Directory (Azure AD).
Integrar o Nimblex com a AD Azure proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso ao Nimblex.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Nimblex (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Nimblex, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura de sinal único Nimblex

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Nimblex apoia **SP** iniciado SSO

* Nimblex suporta o fornecimento de utilizadores **Just In Time**

## <a name="adding-nimblex-from-the-gallery"></a>Adicionando Nimblex da galeria

Para configurar a integração do Nimblex em Azure AD, precisa de adicionar o Nimblex da galeria à sua lista de aplicações saaS geridas.

**Para adicionar Nimblex da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Nimblex**, selecione **Nimblex** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![Nimblex na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on azure ad com o Nimblex com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Nimblex.

Para configurar e testar o único sinal de Azure AD com o Nimblex, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sign-On Single Nimblex](#configure-nimblex-single-sign-on)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie um utilizador](#create-nimblex-test-user)** de teste Nimblex - para ter uma contrapartida de Britta Simon em Nimblex que esteja ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único signo da Azure AD com o Nimblex, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Nimblex,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set-up single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Nimblex Domain e URLs informações únicas de inscrição](common/sp-identifier-reply.png)

    a. Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<YOUR APPLICATION PATH>/Login.aspx`

    b. Na caixa **de identificador,** digite um URL utilizando o seguinte padrão:`https://<YOUR APPLICATION PATH>/`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<path-to-application>/SamlReply.aspx`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL, Identificador e Resposta real. Contacte a equipa de suporte ao [Cliente Nimblex](mailto:support@ebms.com.au) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

4. Na **configuração de um único sign-on com** a página SAML, na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

6. Na secção **Configurar O Nimblex,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-nimblex-single-sign-on"></a>Configure Nimblex Single Sign-On

1. Numa janela de navegador web diferente, inscreva-se no Nimblex como administrador de segurança.

2. No lado superior direito da página, clique no logotipo **Definições.**

    ![Configurações nimblex](./media/nimblex-tutorial/tutorial_nimblex_settings.png)

3. Na página do Painel de **Controlo,** na secção **de segurança** clique em Iniciar um **único sinal**.

    ![Configurações nimblex](./media/nimblex-tutorial/tutorial_nimblex_single.png)

4. Na página **'Gerir Um Único Sinal',** selecione o nome da sua instância e clique em **Editar**.

    ![Nimblex saml](./media/nimblex-tutorial/tutorial_nimblex_saml.png)

5. Na página **Fornecedor Edit SSO,** execute os seguintes passos:

    ![Nimblex saml](./media/nimblex-tutorial/tutorial_nimblex_sso.png)

    a. Na caixa de texto **Descrição,** escreva o nome da sua instância.

    b. No Notepad, abra o certificado codificado base-64 que descarregou do portal Azure, copie o seu conteúdo e, em seguida, cole-o na caixa **de certificado.**

    c. Na caixa de texto sso target url do fornecedor de **identidade Sso Target,** colá o valor do URL de **Login,** que copiou do portal Azure.

    d. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome,** entre **brittaSimon.**
  
    b. No campo de **nome do utilizador,** escreva**brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso ao Nimblex.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações**e, em seguida, selecione **Nimblex**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Nimblex**.

    ![O link Nimblex na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML então no diálogo **Select Role,** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

### <a name="create-nimblex-test-user"></a>Criar o utilizador de teste Nimblex

Nesta secção, um utilizador chamado Britta Simon é criado em Nimblex. O Nimblex suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Nimblex, um novo é criado após a autenticação.

>[!Note]
>Se precisar de criar um utilizador manualmente, contacte a equipa de suporte ao [Cliente Nimblex](mailto:support@ebms.com.au).

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Nimblex no Painel de Acesso, deve ser automaticamente inscrito no Nimblex para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

