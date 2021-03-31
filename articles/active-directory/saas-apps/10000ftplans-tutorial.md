---
title: 'Tutorial: Integração do Diretório Ativo Azure com planos de 10.000 pés | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e os Planos de 10.000 pés.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.openlocfilehash: fc2a2c557ff9738051b9d9f38ad8947d2edd6a36
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92319256"
---
# <a name="tutorial-azure-active-directory-integration-with-10000ft-plans"></a>Tutorial: Integração do Diretório Ativo Azure com planos de 10.000 pés

Neste tutorial, aprende-se a integrar planos de 10.000 pés com o Azure Ative Directory (Azure AD).
Integrar planos de 10.000 pés com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a planos de 10.000 pés.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos em Planos de 10.000ft (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com planos de 10.000 pés, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* 10.000ft Planos única assinatura ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Planos de 10.000 ft apoiam **SP** iniciado SSO
* Suporte de planos de 10.000 pés 

## <a name="adding-10000ft-plans-from-the-gallery"></a>Adicionando planos de 10.000 pés da galeria

Para configurar a integração de planos de 10.000 pés em Ad Azure, você precisa adicionar planos de 10.000 pés da galeria à sua lista de aplicações geridas saaS.

**Para adicionar planos de 10.000 pés da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, clique no botão **de aplicação Novo** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite planos de **10.000 pés**, selecione planos de **10.000 pés** do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Planos de 10.000 pés na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD single sign-on com planos de 10.000 pés com base em um utilizador de teste chamado **Britta Simon**.
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Planos de 10.000 pés.

Para configurar e testar o único sinal de Azure AD com planos de 10.000 pés, você precisa completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure planos de 10000ft Único Sinal-On](#configure-10000ft-plans-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. Crie um utilizador de **[teste de planos de 10000 ft](#create-10000ft-plans-test-user)** - para ter uma contraparte de Britta Simon em planos de 10.000 pés que está ligado à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o único sinal de Ad AD com planos de 10.000 pés, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **de 10.000 ft Plans,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![10.000 ft Planos Domínio e URLs informações únicas de súmis](common/sp-identifier.png)

    a. Na caixa de texto **URL, digite** o URL: `https://app.10000ft.com`

    b. Na caixa de texto **identifier (Entity ID),** digite o URL: `https://app.10000ft.com/saml/metadata`

    > [!NOTE]
    > O valor para **Identificador** é diferente se tiver um domínio personalizado. Contacte a equipa de [apoio ao cliente de 10.000 pés](https://www.10000ft.com/plans/support) para obter este valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Raw)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificateraw.png)

6. Na secção **Configurar planos de 10.000 pés,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-10000ft-plans-single-sign-on"></a>Configurar planos de 10000 pés Sign-On

Para configurar um único sign-on no lado dos planos de **10.000 pés,** você precisa enviar o Certificado descarregado **(Raw)** e URLs copiados apropriados do portal Azure para a equipe de [suporte planos de 10.000 pés](https://www.10000ft.com/plans/support). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome,** **insira BrittaSimon**.
  
    b. No campo **nome do utilizador,** escreva `brittasimon@yourcompanydomain.extension` . Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon use a Azure single sign-on, concedendo acesso a planos de 10.000 pés.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione Planos de **10.000 pés**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Planos de 10.000 pés**.

    ![A ligação de planos de 10.000 pés na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos,** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="create-10000ft-plans-test-user"></a>Criar utilizadores de teste de planos de 10000 pés

Nesta secção, um utilizador chamado Britta Simon é criado em Planos de 10.000 pés. Os Planos de 10.000 pés suportam o provisionamento do utilizador just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir em Planos de 10.000 pés, um novo é criado após a autenticação.

> [!NOTE]
> Se precisar de criar um utilizador manualmente, tem de contactar a equipa de suporte do cliente de [10.000 pés](https://www.10000ft.com/plans/support).

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo de 10.000 pés planos no Painel de Acesso, deverá ser automaticamente inscrito nos Planos de 10.000 pés para os quais configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)