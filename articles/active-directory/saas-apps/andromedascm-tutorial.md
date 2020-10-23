---
title: 'Tutorial: Integração do Azure Ative Directory com a Andromeda Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Andromeda.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 0d342f672f4e8a5fdf33afe0feb7c5051956e29f
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92318780"
---
# <a name="tutorial-azure-active-directory-integration-with-andromeda"></a>Tutorial: Integração do Diretório Ativo Azure com Andromeda

Neste tutorial, aprende-se a integrar Andromeda com o Azure Ative Directory (Azure AD).
A integração de Andromeda com a Ad Azure proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD que tem acesso a Andromeda.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos em Andromeda (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a Andromeda, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura ativada por andromeda

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Andromeda apoia **SP e IDP** iniciaM SSO
* Andromeda suporta **provisão** de utilizadores just in time

## <a name="adding-andromeda-from-the-gallery"></a>Adicionar Andromeda da galeria

Para configurar a integração de Andromeda em Azure AD, você precisa adicionar Andromeda da galeria à sua lista de aplicações geridas saaS.

**Para adicionar Andromeda da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Andromeda,** selecione **Andromeda** do painel de resultados e clique em Adicionar o botão **Adicionar** a aplicação.

    ![Andromeda na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD single sign-on com Andromeda com base em um utilizador de teste chamado **Britta Simon**.
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Andromeda.

Para configurar e testar o Azure AD com Andromeda, tem de completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sign-On Único da Andromeda](#configure-andromeda-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Andromeda test user](#create-andromeda-test-user)** - para ter uma contraparte de Britta Simon em Andromeda que está ligada à representação AZure AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com Andromeda, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Andromeda,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute os seguintes passos:

    ![A screenshot mostra a Configuração BÁSICA SAML, onde pode introduzir o Identificador, Responder U R L e selecionar Guardar.](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<tenantURL>.ngcxpress.com/`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<tenantURL>.ngcxpress.com/SAMLConsumer.aspx`

5. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    ![Screenshot mostra Definir U R Ls adicionais onde pode introduzir um sinal em U R L.](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<tenantURL>.ngcxpress.com/SAMLLogon.aspx`

    > [!NOTE]
    > Estes valores não são reais. Irá atualizar o valor com o identificador real, URL de resposta e URL Sign-On, que é explicado mais tarde no tutorial.

6. A aplicação Andromeda espera as afirmações DOL num formato específico. Configure os seguintes pedidos para este pedido. Pode gerir os valores destes atributos a partir da secção **Atributos** do Utilizador na página de integração de aplicações. Na **configuração single Sign-On com** a página SAML, clique no botão **Editar** para abrir o diálogo **de Atributos do Utilizador.**

    ![A screenshot mostra atributos do utilizador como dado nome utilizador.givenname e emailaddress user.mail.](common/edit-attribute.png)

    > [!Important]
    > Limpe as definições do NameSpace enquanto as configura.

7. Na secção **'Reclamações** de Utilizador' no diálogo **'Atributos do Utilizador',** edite as alegações utilizando o **ícone Editar** ou adicione as alegações utilizando **adicionar uma nova alegação** para configurar o atributo de ficha SAML como mostrado na imagem acima e executar os seguintes passos: 

    | Nome | Atributo de origem|
    | ------ | -----------|
    | papel        | Papel específico da aplicação |
    | tipo        | Tipo de Aplicação |
    | empresa       | CompanyName |

    > [!NOTE]
    > Não há valores reais. Estes valores são apenas para fins de demonstração, por favor use os seus papéis de organização.

    a. Clique **Em Adicionar nova reivindicação** para abrir o diálogo de reclamações do utilizador **Gerir.**

    ![A Screenshot mostra as reclamações do Utilizador com opções para adicionar uma nova reclamação e guardar.](common/new-save-attribute.png)

    ![Screenshot mostra Gerir as alegações do utilizador onde pode introduzir valores descritos I neste passo.](common/new-attribute-details.png)

    b. Na caixa de texto **'Nome',** digite o nome do atributo indicado para esta linha.

    c. Deixe o **Espaço Namespace** em branco.

    d. Selecione Fonte como **Atributo**.

    e. A partir da lista **de atributos Source,** digite o valor de atributo mostrado para esta linha.

    f. Clique **em Ok**

    exemplo, Clique em **Guardar**.

8. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

9. Na secção **Configuração andromeda,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-andromeda-single-sign-on"></a>Configurar Andromeda Single Sign-On

1. Inscreva-se no site da empresa Andromeda como administrador.

2. No topo da barra de menus clique em **Administração** e navegue para a **Administração.**

    ![Administração Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_admin.png)

3. No lado esquerdo da barra de ferramentas na secção **Interfaces,** clique na **configuração SAML**.

    ![Andromeda saml](./media/andromedascm-tutorial/tutorial_andromedascm_saml.png)

4. Na página da secção **de configuração SAML,** execute os seguintes passos:

    ![Andromeda config](./media/andromedascm-tutorial/tutorial_andromedascm_config.png)

    a. Verifique **ativar sSO com SAML**.

    b. Na secção **de Informação de Andromeda,** copie o valor **de identidade SP** e cole-o na caixa de texto **identifier** da secção **de Configuração Básica SAML.**

    c. Copie o valor **url do consumidor** e cole-o na caixa de texto URL de **resposta** da secção **de configuração básica do SAML.**

    d. Copie o valor URL do **logon** e **cole-o na** caixa de texto URL de url de assinatura da secção **de configuração básica do SAML.**

    e. Na secção **SAML Identity Provider,** digite o seu Nome IDP.

    f. Na caixa de texto **Single Sign On End Point,** cole o valor do URL de **login** que, copiou a partir do portal Azure.

    exemplo, Abra o **certificado codificado Base64** descarregado do portal Azure em bloco de notas, cole-o na caixa de texto **do Certificado X 509.**
    
    h. Mapear os seguintes atributos com o respetivo valor para facilitar o login SSO a partir do Azure AD. O atributo **ID do utilizador** é necessário para iniciar sessão. Para provisionamento, são necessários **emails,** **empresas,** **UserType**e **Role.** Nesta secção, definimos atributos de mapeamento (nome e valores) que se relacionam com os definidos dentro do portal Azure

    ![Attbmap andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_attbmap.png)

    i. Clique em **Guardar**.

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

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso a Andromeda.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **Andromeda**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Andromeda**.

    ![O link Andromeda na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-andromeda-test-user"></a>Criar utilizador de teste Andromeda

Nesta secção, um utilizador chamado Britta Simon é criado em Andromeda. A Andromeda suporta o provisionamento do utilizador just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir em Andromeda, um novo é criado após a autenticação. Se precisar de criar um utilizador manualmente, contacte a equipa de [suporte do Cliente Andromeda.](https://www.ngcsoftware.com/support/)

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Andromeda no Painel de Acesso, deverá ser automaticamente inscrito no Andromeda para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)