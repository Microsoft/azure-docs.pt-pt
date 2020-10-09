---
title: 'Tutorial: Integração do Azure Ative Directory com Tableau Online Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Tableau Online.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: 035bc13ee44a8a003ae860eb2bdd67432fa91f14
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88542527"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-online"></a>Tutorial: Azure Ative Directory integração única (SSO) com Tableau Online

Neste tutorial, você vai aprender a integrar Tableau Online com Azure Ative Directory (Azure AD). Quando integra o Tableau Online com a AD Azure, pode:

* Controle em Azure AD que tem acesso ao Tableau Online.
* Ative os seus utilizadores a serem automaticamente inscritos no Tableau Online com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Tableau Online assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Tableau Online suporta **SSO** iniciado SP
* Assim que configurar o Tableau Online, pode impor o controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-tableau-online-from-the-gallery"></a>Adicionar Tableau Online da galeria

Para configurar a integração do Tableau Online no AZure AD, é necessário adicionar tableau Online da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** escreva **Tableau Online** na caixa de pesquisa.
1. Selecione **Tableau Online** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com tableau online com base num utilizador de teste chamado **Britta Simon**.
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Tableau Online.

Para configurar e testar o Azure AD SSO com tableau Online, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Tableau Online SSO](#configure-tableau-online-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Tableau Online test user](#create-tableau-online-test-user)** - para ter uma contraparte de B.Simon em Tableau Online que está ligada à representação AZure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD single sign-on com Tableau Online, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Tableau Online,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Informações de súmica online de domínio e URLs](common/sp-identifier.png)

    a. Na caixa de texto **URL, digite** o URL: `https://sso.online.tableau.com/public/sp/login?alias=<entityid>`

    b. Na caixa de texto **identifier (Entity ID),** digite o URL: `https://sso.online.tableau.com/public/sp/metadata?alias=<entityid>`

    > [!NOTE]
    > Você receberá o `<entityid>` valor da secção **Configurar Tableau Online** neste tutorial. O valor de ID da entidade será o valor **do identificador Azure AD** na secção **Configurar Tableau Online.**

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção **Configurar Tableau Online,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

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
    Por exemplo, BrittaSimon \@ contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso ao Tableau Online.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **Tableau Online**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Tableau Online**.

    ![O link Tableau Online na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

## <a name="configure-tableau-online-sso"></a>Configurar tableau online SSO

1. Numa janela de navegador diferente, inscreva-se na sua aplicação Tableau Online. Ir para **Definições** e, em seguida, **Autenticação**.

    ![Configurar Sign-On Individuais](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)

2. Para ativar a secção SAML, **Tipos de Autenticação Sub-Autenticação.** Verifique **Ativar um método de autenticação adicional** e, em seguida, verifique a caixa de verificação **SAML.**

    ![Configurar Sign-On Individuais](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

3. Desloque-se até ao **ficheiro de metadados de importação para a secção Tableau Online.**  Clique em Procurar e importe o ficheiro metadados, que descarregou a partir de Azure AD. Em seguida, clique **em Aplicar**.

   ![Configurar Sign-On Individuais](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

4. Na secção **de afirmações match, insira** o nome de afirmação do Fornecedor de Identidade correspondente para **endereço de e-mail,** **nome próprio**e **apelido**. Para obter esta informação da Azure AD: 
  
    a. No portal Azure, vá à página de integração de aplicações **Tableau Online.**

    b. Na secção **Atributos & Pedidos** de Utilizador, clique no ícone de edição.

   ![Configurar Sign-On Individuais](./media/tableauonline-tutorial/attributesection.png)

    c. Copie o valor do espaço de nome para estes atributos: nome dado, e-mail e apelido utilizando os seguintes passos:

   ![Azure AD Single Sign-On](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)

    d. Clique no valor **do utilizador.dado nome**

    e. Copie o valor da caixa de texto **Namespace.**

    ![Configurar Sign-On Individuais](./media/tableauonline-tutorial/attributesection2.png)

    f. Para copiar os valores do espaço de nome para o e-mail e sobrenome repita os passos acima.

    exemplo, Mude para a aplicação Tableau Online e, em seguida, desaver a secção **"Atributos do Utilizador & Reclamações** da seguinte forma:

    * Email: **email** ou **userprincipalname**

    * Primeiro nome: **nome dado**

    * Apelido: **apelido**

    ![Configurar Sign-On Individuais](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="create-tableau-online-test-user"></a>Criar utilizador de teste Online Tableau

Nesta secção, cria-se um utilizador chamado Britta Simon in Tableau Online.

1. No **Tableau Online,** clique em **Definições** e, em seguida, na secção **de Autenticação.** Desloque-se para baixo para gerir a secção **de utilizadores.** Clique **em Adicionar Utilizadores** e, em seguida, clique **em Introduzir Endereços de E-mail.**
  
    ![Criar um utilizador de teste AZure AD](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)

2. **Selecione Adicionar os utilizadores para a autenticação (SAML).** Na **caixa de e-mail Enter os endereços** de texto adicionam britta.simon \@ contoso.com
  
    ![Criar um utilizador de teste AZure AD](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)

3. Clique **em Adicionar Utilizadores**.

### <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Do Tableau Online no Painel de Acesso, deverá ser automaticamente inscrito no Tableau Online para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)