---
title: 'Tutorial: Integração do Diretório Ativo Azure com dossier | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Dossier.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/04/2019
ms.author: jeedes
ms.openlocfilehash: ff5da4b87589889172a9518fd67486fe14451630
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92454656"
---
# <a name="tutorial-azure-active-directory-integration-with-dossier"></a>Tutorial: Integração do Diretório Ativo Azure com Dossier

Neste tutorial, aprende-se a integrar o Dossier com o Azure Ative Directory (Azure AD).
Integrar o Dossier com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao Dossier.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Dossier (SÚM's Single) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD azure com o Dossier, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura única ativada do processo de acesso

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Dossiê apoia **SP** iniciado SSO

## <a name="adding-dossier-from-the-gallery"></a>Adicionar dossier da galeria

Para configurar a integração do Dossier em Azure AD, é necessário adicionar o Dossier da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Dossier da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Dossier**, selecione **Dossier** do painel de resultados e, em seguida, clique em Adicionar o botão **Adicionar** a aplicação.

     ![Dossier na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com um único sign-on com dossier baseado num utilizador de teste chamado **Britta Simon**.
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Dossier.

Para configurar e testar o Azure AD com um único sinal de acesso com o Dossier, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o sign-on único do dossier](#configure-dossier-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Dossier test user](#create-dossier-test-user)** - ter uma contrapartida de Britta Simon em Dossier que está ligada à representação AZure AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD um único sinal de inscrição com o Dossier, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **do Dossier,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Informações de assinatura única do domínio do dossiê e URLs](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão:

    ```http
    https://<SUBDOMAIN>.dossiersystems.com/azuresso/account/SignIn
    https://dossier.<CLIENTDOMAINNAME>/azuresso/account/SignIn
    ```

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `Dossier/<CLIENTNAME>`

    > [!NOTE]
    > Para o valor do identificador deve estar no formato ou em `Dossier/<CLIENTNAME>` qualquer valor personalizado do utilizador.

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:
    
    ```http
    https://<SUBDOMAIN>.dossiersystems.com/azuresso
    https://dossier.<CLIENTDOMAINNAME>/azuresso
    ```


    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real em URL, Identifier e URL de resposta. Contacte [a equipa de apoio ao Cliente do Dossier](mailto:support@intellimedia.ca) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

4. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique no botão de cópia para copiar o **Url de Metadados da Federação** de Aplicações a partir das opções dadas de acordo com o seu requisito e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/copy-metadataurl.png)

6. Na secção **set up Dossier,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-dossier-single-sign-on"></a>Configure Dossier Único Sign-On

Para configurar um único sinal no lado **do Dossier,** é necessário enviar o **Url de Metadados da Federação de Aplicações** para a [equipa de apoio](mailto:support@intellimedia.ca)ao Dossier . Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **do nome do utilizador** `brittasimon@yourcompanydomain.extension` . Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon utilize a Azure um único sinal de acesso, concedendo acesso ao Dossier.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **Dossier**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Dossier.**

    ![O link do Dossiê na lista de Candidaturas](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-dossier-test-user"></a>Criar utilizador de teste de dossiê

Nesta secção, cria-se um utilizador chamado Britta Simon in Dossier. Trabalhe com [a equipa de apoio](mailto:support@intellimedia.ca) do Dossier para adicionar os utilizadores na plataforma Dossier. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo do Dossier no Painel de Acesso, deverá ser automaticamente inscrito no Dossier para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)