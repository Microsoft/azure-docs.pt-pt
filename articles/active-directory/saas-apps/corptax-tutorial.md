---
title: 'Tutorial: Integração do Azure Ative Directory com a Corptax Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Corptax.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/02/2019
ms.author: jeedes
ms.openlocfilehash: 056cc023c63ae2cc90f6fcc6ef1222ec8e33cab5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88542705"
---
# <a name="tutorial-azure-active-directory-integration-with-corptax"></a>Tutorial: Integração do Diretório Ativo Azure com a Corptax

Neste tutorial, aprende-se a integrar a Corptax com o Azure Ative Directory (Azure AD).
A integração da Corptax com a Azure AD proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD que tem acesso à Corptax.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Corptax (Sign-on Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com a Corptax, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura ativada por corptax

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Corptax suporta **SSO** iniciado SP

## <a name="adding-corptax-from-the-gallery"></a>Adicionar Corptax da galeria

Para configurar a integração da Corptax no Azure AD, precisa adicionar a Corptax da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar a Corptax da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select_azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise_applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add_new_app.png)

4. Na caixa de pesquisa, escreva **Corptax,** selecione **Corptax** do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Corptax na lista de resultados](common/search_new_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD single sign-on com Corptax com base em um utilizador de teste chamado **Britta Simon**.
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na Corptax.

Para configurar e testar o Azure AD com a Corptax, você precisa completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure Corptax Single Sign-On](#configure-corptax-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Corptax test user](#create-corptax-test-user)** - ter uma contraparte de Britta Simon em Corptax que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD single sign-on com a Corptax, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **da Corptax,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select_sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select_saml_option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit_urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Informações únicas de assinatura de domínio de Corptax e URLs](common/sp_intiated.png)

    Na caixa de texto **URL de entrada de inscrição,** digite um URL:  `https://asp.corptax.com`

5. Na **configuração single Sign-On com página SAML,** Na secção Certificado de Assinatura **SAML,** clique em **Baixar** para descarregar **O Metdata XML da Federação** e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

### <a name="configure-corptax-single-sign-on"></a>Configurar a Sign-On Única de Corptax

Para configurar um único sign-on no lado **da Corptax,** você precisa enviar o **metdata XML da Federação** descarregado para a [equipa de suporte da Corptax](https://connect.corptax.com/). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new_user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user_properties.png)

    a. No campo **Nome,** **insira BrittaSimon**.

    b. No campo **nome do utilizador,** escreva `brittasimon\@yourcompanydomain.extension`  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso à Corptax.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **Corptax**.

    ![Painel Aplicações empresariais](common/enterprise_applications.png)

2. Na lista de aplicações, escreva e selecione **Corptax**.

    ![O link Corptax na lista de Aplicações](common/all_applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users_groups_blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add_assign_user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="create-corptax-test-user"></a>Criar utilizador de teste Corptax

Nesta secção, cria-se um utilizador chamado Britta Simon in Corptax. Trabalhe com a [equipa de apoio da Corptax](https://connect.corptax.com/) para adicionar os utilizadores na plataforma Corptax. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.
Quando clicar no azulejo Corptax no Painel de Acesso, deverá ser redirecionado para a página abaixo da Corptax. 

![image](media/corptax-tutorial/corptaxlogin.png)

Na caixa de texto **Ambiente,** escreva o seu ambiente apropriado, deverá ser automaticamente inscrito no Corptax para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
