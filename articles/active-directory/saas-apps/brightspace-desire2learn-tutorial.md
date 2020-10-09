---
title: 'Tutorial: Integração do Azure Ative Directory com Brightspace by Desire2Learn Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Brightspace by Desire2Learn.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/08/2019
ms.author: jeedes
ms.openlocfilehash: e212b8c72a8cf7000a2789034f3ee83a5a0c9cda
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88542782"
---
# <a name="tutorial-azure-active-directory-integration-with-brightspace-by-desire2learn"></a>Tutorial: Integração do Azure Ative Directory com Brightspace by Desire2Learn

Neste tutorial, você aprende a integrar Brightspace by Desire2Learn com Azure Ative Directory (Azure AD).
A integração do Brightspace by Desire2Learn com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao Brightspace by Desire2Learn.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Brightspace pela Desire2Learn (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD do Azure com o Brightspace by Desire2Learn, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Brightspace by Desire2Learn única subscrição ativada

> [!NOTE]
> Esta integração também está disponível para usar a partir do ambiente cloud do governo dos EUA Azure AD. Você pode encontrar esta aplicação na Azure AD US Government Cloud Application Gallery e configurá-la da mesma forma que você faz a partir de nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Brightspace by Desire2Learn suporta **IDP** iniciado SSO

## <a name="adding-brightspace-by-desire2learn-from-the-gallery"></a>Adicionando Brightspace por Desire2Learn da galeria

Para configurar a integração do Brightspace by Desire2Learn em AD Azure, é necessário adicionar Brightspace by Desire2Learn da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Brightspace by Desire2Learn da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **escreva Brightspace by Desire2Learn,** selecione **Brightspace by Desire2Learn** do painel de resultados e clique em Adicionar botão **Adicionar** a aplicação.

     ![Brightspace by Desire2Learn na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD single sign-on com Brightspace by Desire2Learn com base num utilizador de teste chamado **Britta Simon**.
Para um único início de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Brightspace by Desire2Learn.

Para configurar e testar o único sinal de Azure AD com Brightspace by Desire2Learn, você precisa completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure Brightspace by Desire2Learn Single Sign-On](#configure-brightspace-by-desire2learn-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Brightspace by Desire2Learn test user](#create-brightspace-by-desire2learn-test-user)** - para ter uma contraparte de Britta Simon em Brightspace by Desire2Learn que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o único sign-on Ad Ad ad com Brightspace by Desire2Learn, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Brightspace by Desire2Learn,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na **configuração single Sign-On com página SAML,** execute os seguintes passos:

    ![Brightspace by Desire2Learn Domain e URLs informações únicas de início de súrbio](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:
    
    ```http
    https://<companyname>.tenants.brightspace.com/samlLogin
    https://<companyname>.desire2learn.com/shibboleth-sp
    ```

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<companyname>.desire2learn.com/d2l/lp/auth/login/samlLogin.d2l`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e resposta real. Contacte [a equipa de suporte do Cliente Desire2Learn](https://www.d2l.com/contact/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção **Configurar Brightspace by Desire2Learn,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure Ad

    c. Logout URL

### <a name="configure-brightspace-by-desire2learn-single-sign-on"></a>Configure Brightspace por Desire2Learn Single Sign-On

Para configurar um único sign-on no Brightspace pelo lado **Desire2Learn,** você precisa enviar o **Metdata XML da Federação** descarregado e URLs copiados apropriados do portal Azure para [Brightspace pela equipa de suporte Desire2Learn](https://www.d2l.com/contact/). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **nome de utilizador** **brittasimon \@ yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso ao Brightspace by Desire2Learn.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **Brightspace by Desire2Learn**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Brightspace by Desire2Learn**.

    ![O link Brightspace by Desire2Learn na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-brightspace-by-desire2learn-test-user"></a>Criar Brightspace por Desire2Learn utilizador de teste

Nesta secção, cria-se um utilizador chamado Britta Simon in Brightspace by Desire2Learn. Trabalhe com [a equipa de suporte Brightspace by Desire2Learn](https://www.d2l.com/contact/) para adicionar os utilizadores na plataforma Brightspace by Desire2Learn. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

> [!NOTE]
> Pode utilizar qualquer outra ferramenta de criação de conta de utilizador Brightspace by Desire2Learn ou APIs fornecidas pelo Brightspace by Desire2Learn para fornecer contas de utilizadores do Azure Ative Directory.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Brightspace by Desire2Learn no Painel de Acesso, deverá ser automaticamente inscrito no Brightspace by Desire2Learn para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)