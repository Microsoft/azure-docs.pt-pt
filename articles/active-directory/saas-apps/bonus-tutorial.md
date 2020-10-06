---
title: 'Tutorial: Integração do Azure Ative Directory com bónus Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Bonusly.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: 7c1bd1b3f9931eff2f19eac7b78881d4f7ad8ae0
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91744326"
---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Tutorial: Integração do Azure Ative Directory com bónus

Neste tutorial, aprende-se a integrar bónus com o Azure Ative Directory (Azure AD).
A integração de Bónus com a AD Azure proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Bonusly.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Bónus (Sign-on Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com a Bonusly, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Bónus assinatura ativada por um único sinal

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Bónus suporta **IDP** iniciado SSO

## <a name="adding-bonusly-from-the-gallery"></a>Adicionar bónus da galeria

Para configurar a integração do Bonusly no AD Azure, precisa adicionar Bonusly da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Bonusly da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **escreva Bonusly**, selecione **Bonusly** do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Bónus na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com bónus baseado num utilizador de teste chamado **Britta Simon**.
Para um único sinal de acesso ao trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Bonusly.

Para configurar e testar o Azure AD com bónus, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure bónus bónus único sinal -para](#configure-bonusly-single-sign-on)** configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Bonusly test user](#create-bonusly-test-user)** - para ter uma contraparte de Britta Simon em Bonusly que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com bónus, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Bonusly,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração de 'Sessão única' com** a página SAML, clique em **Editar** o ícone para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Bónus Domínio e URLs informações únicas de acesso](common/idp-reply.png)

    Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:  `https://Bonus.ly/saml/<tenant-name>`

    > [!NOTE]
    > O valor não é real. Atualizar o valor com o URL de resposta real. Contacte [a equipa de suporte do Cliente Bonusly](https://bonus.ly/contact) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na secção **Certificado de Assinatura SAML,** clique em Editar o botão **Editar** para abrir o diálogo **do Certificado de Assinatura SAML.**

    ![Editar certificado de assinatura SAML](common/edit-certificate.png)

6. Na secção **Certificado de Assinatura SAML,** copie a **IMPRESSÃO DIGITAL** e guarde-a no seu computador.

    ![Valor da impressão digital do polegar da cópia](common/copy-thumbprint.png)

7. Na secção **Configurar Bónus,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-bonusly-single-sign-on"></a>Configurar bónus bónus único sinal-on

1. Numa janela de navegador diferente, inscreva-se no seu inquilino **Bonusly.**

1. Na barra de ferramentas na parte superior, clique em **Definições** e, em seguida, **selecione Integrações e aplicações**.

    ![Secção Social Bónus](./media/bonus-tutorial/ic773686.png "Bonusly")
1. Em **'SÚM' único**, selecione **SAML**.

1. Na página de diálogo **SAML,** execute os seguintes passos:

    ![Página de diálogo saml bónus](./media/bonus-tutorial/ic773687.png "Bonusly")

    a. Na caixa de texto **do URL do IdP SSO,** cole o valor do URL de **login,** que copiou do portal Azure.

    b. Na caixa de texto **url de login IdP,** cole o valor do URL de **Login,** que copiou do portal Azure.

    c. Na caixa de texto **do Emitente IdP,** cole o valor do **Identificador AD Azure,** que copiou do portal Azure.
    
    d. Cole o valor de **impressão digital** digital copiado do portal Azure para a caixa de texto da impressão **digital Cert.**
    
    e. Clique em **Guardar**.

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

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso a Bonusly.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **Bonusly**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Bonusly**.

    ![O link bónus na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-bonusly-test-user"></a>Criar utilizador de teste bónus

Para permitir que os utilizadores Azure AD inscrevam-se na Bonusly, devem ser adustados em Bónus. No caso de Bónus, o provisionamento é uma tarefa manual.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador bónus ou APIs fornecidas pela Bonusly para fornecer contas de utilizador Azure AD. 

**Para configurar o provisionamento do utilizador, execute os seguintes passos:**

1. Numa janela do navegador web, inscreva-se no seu inquilino Bonusly.

1. Clique em **Definições**.

    ![Definições](./media/bonus-tutorial/ic781041.png "Definições")

1. Clique no separador **Utilizadores e bónus.**

    ![Utilizadores e bónus](./media/bonus-tutorial/ic781042.png "Utilizadores e bónus")

1. Clique **em Gerir utilizadores.**

    ![Gerir utilizadores](./media/bonus-tutorial/ic781043.png "Gerir Utilizadores")

1. Clique em **Adicionar Utilizador**.

    ![A screenshot mostra gerir os utilizadores onde pode selecionar Adicionar Utilizador.](./media/bonus-tutorial/ic781044.png "Adicionar Utilizador")

1. No diálogo **do Utilizador Adicionar,** execute os seguintes passos:

    ![A screenshot mostra a caixa de diálogo Do Utilizador Adicionar onde pode introduzir estas informações.](./media/bonus-tutorial/ic781045.png "Adicionar Utilizador")  

    a. Na caixa de texto do **primeiro nome,** insira o primeiro nome de utilizador como **Britta**.

    b. Na caixa de texto **do último nome,** insira o último nome de utilizador como **Simon**.

    c. Na caixa de sms **do Email,** insira o e-mail do utilizador como `brittasimon\@contoso.com` .

    d. Clique em **Guardar**.

    > [!NOTE]
    > O titular da conta Azure AD recebe um e-mail que inclui um link para confirmar a conta antes de ficar ativa.  

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Bonusly no Painel de Acesso, deverá ser automaticamente inscrito no Bonusly para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
