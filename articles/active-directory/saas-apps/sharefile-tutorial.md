---
title: 'Tutorial: Integração do Azure Ative Directory com o Citrix ShareFile Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Citrix ShareFile.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.openlocfilehash: 0535363165ccda83759a21bff1773280e60fe73c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88542603"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Tutorial: Integração do Azure Ative Directory com o Citrix ShareFile

Neste tutorial, aprende-se a integrar o Citrix ShareFile com o Azure Ative Directory (Azure AD).
A integração do Citrix ShareFile com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao Citrix ShareFile.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Citrix ShareFile (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com o Citrix ShareFile, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Citrix ShareFile assinatura única ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Citrix ShareFile suporta **SSO** iniciado SP

## <a name="adding-citrix-sharefile-from-the-gallery"></a>Adicionar Citrix ShareFile da galeria

Para configurar a integração do Citrix ShareFile no AD Azure, é necessário adicionar o Citrix ShareFile da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Citrix ShareFile da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Citrix ShareFile**, selecione **Citrix ShareFile** do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

     ![Citrix ShareFile na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com citrix ShareFile com base num utilizador de teste chamado **Britta Simon**.
Para um único sinal de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Citrix ShareFile.

Para configurar e testar o Azure AD com citrix ShareFile, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configurar citrix ShareFile Single Sign-On](#configure-citrix-sharefile-single-sign-on)** - para configurar as definições de Sign-On único no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Citrix ShareFile test user](#create-citrix-sharefile-test-user)** - para ter uma contraparte de Britta Simon no Citrix ShareFile que está ligada à representação AD Ad Azure do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com citrix ShareFile, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Citrix ShareFile,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração de 'Sessão única' com** a página SAML, clique em **Editar** o ícone para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Citrix ShareFile Domain e URLs informações únicas de súmis](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://<tenant-name>.sharefile.com/saml/login`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão:

    - `https://<tenant-name>.sharefile.com`
    - `https://<tenant-name>.sharefile.com/saml/info`
    - `https://<tenant-name>.sharefile1.com/saml/info`
    - `https://<tenant-name>.sharefile1.eu/saml/info`
    - `https://<tenant-name>.sharefile.eu/saml/info`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:
    
    - `https://<tenant-name>.sharefile.com/saml/acs`
    - `https://<tenant-name>.sharefile.eu/saml/<URL path>`
    - `https://<tenant-name>.sharefile.com/saml/<URL path>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de inscrição, identificação e URL de resposta. Contacte [a equipa de suporte do Cliente Citrix ShareFile](https://www.citrix.co.in/products/citrix-content-collaboration/support.html) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

4. Na **configuração de 'Sessão Única' com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Configurar Citrix ShareFile,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure Ad

    c. Logout URL

### <a name="configure-citrix-sharefile-single-sign-on"></a>Configurar Citrix ShareFile Single Sign-On

1. Numa janela diferente do navegador web, inicie sessão no site da empresa **Citrix ShareFile** como administrador.

2. Na barra de ferramentas em cima, clique em **Administração**.

3. No painel de navegação esquerdo, selecione **Configurar O Sign-On Único**.
   
    ![Administração de Conta](./media/sharefile-tutorial/ic773627.png "Administração de Conta")

4. Na página de diálogo **de configuração de assinatura única/ SAML 2.0** em **Definições Básicas,** execute os seguintes passos:
   
    ![Início de sessão único](./media/sharefile-tutorial/ic773628.png "Início de sessão único")
   
    a. Clique **em Ativar o SAML**.
    
    b. Na sua caixa de texto **IDP Emitente/ID de entidade,** cole o valor do **Identificador Ad Azure** que copiou do portal Azure.

    c. Clique em **Alterar** ao lado do campo **certificado X.509** e, em seguida, faça o upload do certificado que descarregou a partir do portal Azure.
    
    d. Na caixa de texto **URL de login,** cole o valor do URL de **login** que copiou do portal Azure.
    
    e. Na caixa **de textos LOGOUT URL,** cole o valor do **URL logout** que copiou do portal Azure.

5. Clique em **Guardar** no portal de gestão Citrix ShareFile.

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

Nesta secção, você permite que Britta Simon utilize o Azure single sign-on, concedendo acesso ao Citrix ShareFile.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **Citrix ShareFile**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, escreva e selecione **Citrix ShareFile**.

    ![O link Citrix ShareFile na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-citrix-sharefile-test-user"></a>Criar utilizador de teste Citrix ShareFile

Para permitir que os utilizadores de Azure AD acedam ao Citrix ShareFile, devem ser a provisionados no Citrix ShareFile. No caso do Citrix ShareFile, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Faça login no seu inquilino **Citrix ShareFile.**

2. Clique **em Gerir utilizadores gerir \> utilizadores em casa + criar \> empregado.**
   
    ![Criar Empregado](./media/sharefile-tutorial/IC781050.png "Criar Empregado")

3. Na secção **informações básicas,** execute abaixo dos passos:
   
    ![Informação Básica](./media/sharefile-tutorial/IC799951.png "Informação Básica")
   
    a. Na caixa de texto **do Endereço de E-mail,** digite o endereço de e-mail de Britta Simon como ** \@ contoso.com**.
   
    b. Na caixa de texto **Name Name,** escreva **o primeiro nome** do utilizador como **Britta**.
   
    c. Na caixa de texto **Do Último Nome,** escreva **o apelido** do utilizador como **Simon**.

4. Clique em **Adicionar Utilizador**.
  
    >[!NOTE]
    >O titular da conta AZure AD receberá um e-mail e seguirá um link para confirmar a sua conta antes de ficar ativa. Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador Citrix ShareFile ou APIs fornecidas pela Citrix ShareFile para fornecer contas de utilizador Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Citrix ShareFile no Painel de Acesso, deverá ser automaticamente inscrito no Citrix ShareFile para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

