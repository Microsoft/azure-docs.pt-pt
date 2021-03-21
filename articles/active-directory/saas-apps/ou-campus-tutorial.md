---
title: 'Tutorial: Integração do Diretório Ativo Azure com o Campus da OU | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Campus da U.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.openlocfilehash: af80d68f384869e79ef540d41a6707040dd19f2b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92514134"
---
# <a name="tutorial-azure-active-directory-integration-with-ou-campus"></a>Tutorial: Integração do Diretório Ativo Azure com o Campus da OU

Neste tutorial, aprende-se a integrar o Campus da U com o Azure Ative Directory (Azure AD).
A integração do Campus da U com a AD Azure proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao Campus da U.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Campus da OU (Sign-on Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com o Campus da OU, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Subscrição ativada pelo Campus da OU

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Campus da OU apoia SSO iniciado **pela SP**

## <a name="adding-ou-campus-from-the-gallery"></a>Adicionar campus da OU da galeria

Para configurar a integração do Campus da OU em Azure AD, você precisa adicionar o CAMPUS ou da galeria à sua lista de aplicações geridas saaS.

**Para adicionar o Campus ou da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **escreva OU Campus**, selecione **OU Campus** a partir do painel de resultados e, em seguida, clique em Adicionar o botão **Adicionar** a aplicação.

     ![Campus da UO na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD single sign-on com o OU Campus com base em um utilizador de teste chamado **Britta Simon**.
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Campus da OU.

Para configurar e testar o Azure AD com um único sinal de acesso com o Campus da OU, você precisa completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Único Sign-On do Campus OU](#configure-ou-campus-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create OU Campus test user](#create-ou-campus-test-user)** - ter uma contraparte de Britta Simon no Campus da OU que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com um único sign-on com o Campus da OU, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **do CAMPUS da OU,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Ou Campus Domain e URLs informações únicas de súmis](common/sp-intiated.png)

    Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://a.cms.omniupdate.com/<Instance Name>`

    > [!Note]
    > O valor não é real. Atualizar o valor com o sinal real no URL. Contacte [a equipa de suporte do Cliente do Campus da OU](mailto:support@omniupdate.com) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção **Configurar o Campus da OU,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure Ad

    c. Logout URL

### <a name="configure-ou-campus-single-sign-on"></a>Configurar Sign-On Única do Campus ou

Para configurar um único sign-on no lado **do Campus da OU,** você precisa enviar os **metadados XML da Federação** descarregados e URLs copiados apropriados do portal Azure para a equipe de [suporte do CAMPUS OU](mailto:support@omniupdate.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

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

Nesta secção, você permite que Britta Simon use a Azure single sign-on, concedendo acesso ao Campus da OU.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **OU Campus**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **OU Campus**.

    ![O link do Campus da UO na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-ou-campus-test-user"></a>Criar utilizador de teste do Campus da OU

Nesta secção, cria-se um utilizador chamado Britta Simon no Campus da U. Trabalhe com [a equipa de suporte do CAMPUS da OU](mailto:support@omniupdate.com) para adicionar os utilizadores na plataforma do Campus da OU. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo do Campus da OU no Painel de Acesso, deverá ser automaticamente inscrito no Campus da UO para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)