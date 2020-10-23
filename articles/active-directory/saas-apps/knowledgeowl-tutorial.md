---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com KnowledgeOwl Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o KnowledgeOwl.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.openlocfilehash: 367f6713102912786ce258c471278373636f7326
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92458919"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-knowledgeowl"></a>Tutorial: Azure Ative Directory integração única (SSO) com KnowledgeOwl

Neste tutorial, você vai aprender a integrar o KnowledgeOwl com o Azure Ative Directory (Azure AD). Quando integra o KnowledgeOwl com a AD Azure, pode:

* Controlo em Azure AD que tem acesso ao KnowledgeOwl.
* Ative os seus utilizadores a serem automaticamente inscritos no KnowledgeOwl com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Inscrição única ativada knowledgeOwl (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* KnowledgeOwl apoia **SP e IDP** iniciado SSO
* KnowledgeOwl suporta **o fornecimento de utilizadores just in time**

## <a name="adding-knowledgeowl-from-the-gallery"></a>Adicionar KnowledgeOwl da galeria

Para configurar a integração do KnowledgeOwl no AD Azure, é necessário adicionar o KnowledgeOwl da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **escreva KnowledgeOwl** na caixa de pesquisa.
1. Selecione **KnowledgeOwl** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-knowledgeowl"></a>Configurar e testar Azure AD único sign-on para KnowledgeOwl

Configure e teste Azure AD SSO com KnowledgeOwl usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no KnowledgeOwl.

Para configurar e testar a Azure AD SSO com KnowledgeOwl, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o KnowledgeOwl SSO](#configure-knowledgeowl-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create KnowledgeOwl test user](#create-knowledgeowl-test-user)** - para ter uma contraparte de B.Simon in KnowledgeOwl que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração da aplicação **KnowledgeOwl,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:
    
    ```http
    https://app.knowledgeowl.com/sp
    https://app.knowledgeowl.com/sp/id/<unique ID>
    ```

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:
    
    ```http
    https://subdomain.knowledgeowl.com/help/saml-login
    https://subdomain.knowledgeowl.com/docs/saml-login
    https://subdomain.knowledgeowl.com/home/saml-login
    https://privatedomain.com/help/saml-login
    https://privatedomain.com/docs/saml-login
    https://privatedomain.com/home/saml-login
    ```

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:
    
    ```http
    https://subdomain.knowledgeowl.com/help/saml-login
    https://subdomain.knowledgeowl.com/docs/saml-login
    https://subdomain.knowledgeowl.com/home/saml-login
    https://privatedomain.com/help/saml-login
    https://privatedomain.com/docs/saml-login
    https://privatedomain.com/home/saml-login
    ```

    > [!NOTE]
    > Estes valores não são reais. Você precisará atualizar estes valores a partir de identificador real, URL de resposta e URL Sign-On que é explicado mais tarde no tutorial.

1. A aplicação KnowledgeOwl espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação KnowledgeOwl espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Name | Atributo de origem | Espaço de Nomes |
    | ------------ | -------------------- | -----|
    | ssoid | user.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o Certificado **(Raw)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificateraw.png)

1. Na secção **set up KnowledgeOwl,** copie os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao KnowledgeOwl.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **KnowledgeOwl**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-knowledgeowl-sso"></a>Configure KnowledgeOwl SSO

1. Numa janela diferente do navegador web, inscreva-se no site da empresa KnowledgeOwl como administrador.

1. Clique em **Definições** e, em seguida, **selecione Segurança**.

    ![A screenshot mostra segurança selecionada a partir do menu Definições.](./media/knowledgeowl-tutorial/configure1.png)

1. Percorra a **INTEGRAÇÃO SSO SAML** e execute os seguintes passos:

    ![O Screenshot mostra a INTEGRAÇÃO SAML S S O onde pode fazer as alterações descritas aqui.](./media/knowledgeowl-tutorial/configure2.png)

    a. Selecione **Ative SAML SSO**.

    b. Copie o valor **de ID da Entidade SP** e cole-o no **Identificador (ID da Entidade)** na secção **de Configuração SAML Básica** no portal Azure.

    c. Copie o valor **URL do SR Ersiná-lo** e **cole-o nas** caixas de texto URL e URL de resposta na secção **configuração BÁSICA SAML** no portal Azure.

    d. Na **caixa de textoid da entidade IdP,** cole o valor do **identificador Azure AD,** que copiou do portal Azure.

    e. Na caixa de texto **url do IdP Login,** cole o valor URL de **login,** que copiou a partir do portal Azure.

    f. Na caixa de texto **IdP Logout URL,** cole o valor **URL logout,** que copiou a partir do portal Azure

    exemplo, Faça o upload do formulário de certificado descarregado do portal Azure clicando no **Certificado de IdP de upload**.

    h. Clique em **Atributos SAML do mapa** para mapear atributos e execute os seguintes passos:

    ![A screenshot mostra atributos SAML do mapa onde pode fazer as alterações descritas aqui.](./media/knowledgeowl-tutorial/configure3.png)

    * `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ssoid`Introduza na caixa de texto **SSO ID**
    * `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`Introduza a caixa de texto **username/Email.**
    * `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`Introduza na caixa de texto **do primeiro nome.**
    * `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`Introduza na caixa de texto **do último nome.**
    * Clique em **Guardar**

    i. Clique em **Guardar** na parte inferior da página.

    ![Captura de ecrã a mostrar o botão Guardar.](./media/knowledgeowl-tutorial/configure4.png)

### <a name="create-knowledgeowl-test-user"></a>Criar utilizador de teste KnowledgeOwl

Nesta secção, um utilizador chamado B.Simon é criado no KnowledgeOwl. O KnowledgeOwl suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no KnowledgeOwl, um novo é criado após a autenticação.

> [!Note]
> Se precisar de criar um utilizador manualmente, contacte a equipa de [suporte da KnowledgeOwl.](mailto:support@knowledgeowl.com)

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo KnowledgeOwl no Painel de Acesso, deverá ser automaticamente inscrito no KnowledgeOwl para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente KnowledgeOwl com Azure AD](https://aad.portal.azure.com/)