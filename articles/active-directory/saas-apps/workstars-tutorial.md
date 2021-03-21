---
title: 'Tutorial: Integração do Azure Ative Directory com | workstars Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Workstars.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 9f53072b106bedb8e49ba7f3728f39137f848a58
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92895023"
---
# <a name="tutorial-azure-active-directory-integration-with-workstars"></a>Tutorial: Integração do Azure Ative Directory com a Workstars

Neste tutorial, aprende-se a integrar as Workstars com o Azure Ative Directory (Azure AD).
A integração de Workstars com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Workstars.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Workstars (Sign-on único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com a Workstars, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição ativada por workstars

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Workstars apoia **IDP** iniciado SSO

## <a name="adding-workstars-from-the-gallery"></a>Adicionar Workstars da galeria

Para configurar a integração de Workstars em Azure AD, precisa adicionar Workstars da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Workstars da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Workstars**, selecione **Workstars** do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

     ![Workstars na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com uma única sação com workstars com base num utilizador de teste chamado **Britta Simon**.
Para um único início de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Workstars.

Para configurar e testar o Azure AD com uma única súmula com as Workstars, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sign-on single --](#configure-workstars-single-sign-on)** para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Workstars test user](#create-workstars-test-user)** - para ter uma contraparte de Britta Simon em Workstars que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com uma única sação com as Workstars, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Workstars,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na **configuração single Sign-On com página SAML,** execute os seguintes passos:

    ![Workstars Domain e URLs informações únicas de início de súm na súm numa única súm numa única informação](common/idp-intiated.png)

    a. Na caixa de texto **identifier,** digite um URL: `https://workstars.com`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<subdomain>.workstars.com/saml/login_check`

    > [!NOTE]
    > O valor não é real. Atualizar o valor com o URL de resposta real. Contacte [a equipa de suporte do cliente da Workstars](http://support.workstars.com/) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Configurar Workstars,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-workstars-single-sign-on"></a>Configurar workstars single Sign-On

1. Em outra janela do navegador, inscreva-se no site da empresa Workstars como administrador.

2. Na barra de ferramentas principal, clique em **Definições**.

    ![A imagem mostra o botão Definições.](./media/workstars-tutorial/tutorial_workstars_sett.png)

3. Ir para **assinar as**  >  **definições**.

    ![Signon workstars](./media/workstars-tutorial/tutorial_workstars_signon.png)

    ![A screenshot mostra o sinal único na secção onde pode selecionar Definições.](./media/workstars-tutorial/tutorial_workstars_settings.png)

4. Na **página Single Sign On (SAML) - Definições,** execute os seguintes passos:
    
    ![Workstars saml](./media/workstars-tutorial/tutorial_workstars_saml.png)

    a. Na caixa de texto **name do fornecedor de** identidade, tipo Office **365**.

    b. Na caixa de texto **ID da Entidade fornecedora** de identidade, cole o valor do **Identificador AD Azure,** que copiou do portal Azure.

    c. Copie o conteúdo do ficheiro de certificado descarregado no bloco de notas e, em seguida, cole-o na caixa de texto **do Certificado X509.** 

    d. Na caixa de texto **URL SSO SAML,** cole o valor do URL de **Login,** que copiou do portal Azure.
    
    e. Na caixa de texto **url de logout remoto,** cole o valor do **URL logout,** que copiou do portal Azure. 

    f. selecione **ID de nome** como **E-mail (Predefinição)**.

    exemplo, Clique em **Confirmar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **do nome do utilizador** brittasimon@yourcompanydomain.extension . Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso a Workstars.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações** e, em seguida, selecione **Workstars**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Workstars**.

    ![O link Workstars na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-workstars-test-user"></a>Criar utilizador de teste workstars

Nesta secção, cria-se um utilizador chamado Britta Simon in Workstars. Trabalhe com a [equipa de suporte da Workstars](http://support.workstars.com) para adicionar os utilizadores na plataforma Workstars.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo workstars no Painel de Acesso, deverá ser automaticamente inscrito nas Workstars para as quais configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)