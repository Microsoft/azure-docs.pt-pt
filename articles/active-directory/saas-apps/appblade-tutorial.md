---
title: 'Tutorial: Integração do Azure Ative Directory com a AppBlade | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o AppBlade.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: 49fa266319423b4c1a0bc56ad2a32b49c18f168e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97672632"
---
# <a name="tutorial-azure-active-directory-integration-with-appblade"></a>Tutorial: Integração do Azure Ative Directory com a AppBlade

Neste tutorial, aprende-se a integrar o AppBlade com o Azure Ative Directory (Azure AD).
A integração do AppBlade com a AD Azure proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a AppBlade.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no AppBlade (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com o AppBlade, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Subscrição ativada única appBlade

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* AppBlade suporta **SSO** iniciado SP
* AppBlade suporta **provisão de** utilizadores just in time

## <a name="adding-appblade-from-the-gallery"></a>Adicionar AppBlade da galeria

Para configurar a integração do AppBlade no AD Azure, precisa adicionar o AppBlade da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar AppBlade da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite AppBlade,** selecione **AppBlade** do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![AppBlade na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com um único sign-on com AppBlade baseado num utilizador de teste chamado **Britta Simon**.
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no AppBlade.

Para configurar e testar o Azure AD com um único sinal de acesso com o AppBlade, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sign-On Único appBlade](#configure-appblade-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create AppBlade test user](#create-appblade-test-user)** - para ter uma contraparte de Britta Simon em AppBlade que está ligada à representação AZure AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com um único sign-on com appBlade, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **AppBlade,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Informações de assinatura única do Domínio AppBlade e URLs](common/sp-signonurl.png)

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<companyname>.appblade.com/saml/<tenantid>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de Sign-On real. Contacte [a equipa de suporte do Cliente AppBlade](mailto:support@appblade.com) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção **Configurar AppBlade,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure Ad

    c. Logout URL

### <a name="configure-appblade-single-sign-on"></a>Configurar Sign-On Single AppBlade

Para configurar um único sign-on no lado **appBlade,** você precisa enviar o **metdata XML da Federação** descarregado e URLs copiados apropriados do portal Azure para a equipe de [suporte AppBlade](mailto:support@appblade.com). Além disso, peça-lhes para configurar o URL do **emitente SSO** como `https://appblade.com/saml` . Esta definição é necessária para que um único início de sposição funcione.

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

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso ao AppBlade.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações** e, em seguida, selecione **AppBlade**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **AppBlade**.

    ![O link AppBlade na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-appblade-test-user"></a>Criar utilizador de teste AppBlade

O objetivo desta secção é criar um utilizador chamado Britta Simon em AppBlade. O AppBlade suporta o provisionamento just-in-time, que é por defeito ativado. **Certifique-se de que o seu nome de domínio está configurado com AppBlade para o fornecimento do utilizador. Depois disso, apenas o provisionamento do utilizador just-in-time funciona.**

Se o utilizador tiver um endereço de e-mail que termina com o domínio configurado pela AppBlade para a sua conta, então o utilizador irá automaticamente juntar-se à conta como membro com o nível de permissão especificado, que é um dos "Basic" (um utilizador básico que só pode instalar aplicações), "Team Member" (um utilizador que pode carregar novas versões de aplicações e gerir projetos) ou "Administrador" (privilégios admin completos na conta). Normalmente, escolheria o Básico e, em seguida, promoveria os utilizadores manualmente através de um login de Admin (o AppBlade precisa de configurar um login de administração baseado em e-mail com antecedência ou promover um utilizador em nome do cliente após o login).

Não há nenhum item de ação para si nesta secção. Um novo utilizador é criado durante uma tentativa de aceder ao AppBlade se ainda não existir.

> [!NOTE]
> Se precisar de criar um utilizador manualmente, tem de contactar a equipa de suporte da [AppBlade.](mailto:support@appblade.com)

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo AppBlade no Painel de Acesso, deverá ser automaticamente inscrito no AppBlade para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)