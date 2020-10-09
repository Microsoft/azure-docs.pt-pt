---
title: 'Tutorial: Integração do Azure Ative Directory com a Replicon Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Replicon.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.openlocfilehash: 8394191820226a0d4fdcfe1a078e85e1caafa37f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88534338"
---
# <a name="tutorial-integrate-replicon-with-azure-active-directory"></a>Tutorial: Integrar a Replicon com o Azure Ative Directory

Neste tutorial, você vai aprender a integrar Replicon com Azure Ative Directory (Azure AD). Quando integrar o Replicon com a Azure AD, pode:

* Controlo em Azure AD que tem acesso à Replicon.
* Permitir que os seus utilizadores sejam automaticamente inscritos na Replicon com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode ter um mês de teste gratuito [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Replicon assinatura única (SSO) ativada.

> [!NOTE]
> Esta integração também está disponível para usar a partir do ambiente cloud do governo dos EUA Azure AD. Você pode encontrar esta aplicação na Azure AD US Government Cloud Application Gallery e configurá-la da mesma forma que você faz a partir de nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste. Replicon suporta SSO iniciado **SP.**

## <a name="adding-replicon-from-the-gallery"></a>Adicionar Replicon da galeria

Para configurar a integração da Replicon no AZure AD, é necessário adicionar o Replicon da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite Replicon** na caixa de pesquisa.
1. Selecione **Replicon** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Configure e teste Azure AD SSO com Replicon usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Replicon.

Para configurar e testar a Azure AD SSO com a Replicon, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure Replicon SSO](#configure-replicon-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
5. **[Create Replicon test user](#create-replicon-test-user)** - para ter uma contraparte de B.Simon em Replicon que está ligada à representação AZure AD do utilizador.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Replicon,** encontre a secção **Gerir** e selecione **'Único sinal de s-on'.**
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na página **de Configuração Básica SAML,** insira os valores para os seguintes campos:

    1. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://global.replicon.com/!/saml2/<client name>/sp-sso/post`

    1. Na caixa **identifier,** digite um URL utilizando o seguinte padrão: `https://global.replicon.com/!/saml2/<client name>`

    1. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://global.replicon.com/!/saml2/<client name>/sso/post`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de Sign-On real, identifier e responder URL. Contacte a [equipa de suporte do Cliente Replicon](https://www.replicon.com/customerzone/contact-support) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Clique no ícone edit/pen para **o Certificado de Assinatura SAML** para editar as definições.

    ![Algoritmo de assinatura](common/signing-algorithm.png)

    1. Selecione **a afirmação do SIGN SAML** como **opção de assinatura**.

    1. Selecione **SHA-256** como o **algoritmo de assinatura**.

1. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

   ![O link de descarregamento de certificado](common/metadataxml.png)

### <a name="configure-replicon-sso"></a>Configurar replicon Sso

1. Numa janela diferente do navegador web, inscreva-se no site da empresa Replicon como administrador.

2. Para configurar o SAML 2.0, execute os seguintes passos:

    ![Ativar a autenticação SAML](./media/replicon-tutorial/ic777805.png "Ativar a autenticação SAML")

    a. Para exibir o diálogo **EnableSAML Authentication2,** apúgue o seguinte ao SEU URL, após a tecla da empresa: `/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

    * O seguinte mostra o esquema do URL completo: `https://na2.replicon.com/\<YourCompanyKey\>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

   b. Clique na **+** secção para expandir a secção **v20Configuration.**

   c. Clique na **+** secção para expandir a secção **MeDataConfiguration.**

   d. Selecione **SHA256** para xmlSignatureAlgorithm

   e. Clique **em Escolher Ficheiro**, para selecionar o ficheiro XML do seu fornecedor de identidade e clique em Enviar por **isso.**

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `BrittaSimon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Replicon.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Replicon**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="create-replicon-test-user"></a>Criar utilizador de teste replicon

O objetivo desta secção é criar um utilizador chamado B.Simon em Replicon.

**Se precisar de criar o utilizador manualmente, execute os seguintes passos:**

1. Numa janela do navegador web, inscreva-se no site da empresa Replicon como administrador.

2. Vá aos ** \> Utilizadores da Administração.**

    ![Utilizadores](./media/replicon-tutorial/ic777806.png "Utilizadores")

3. Clique **em +Adicionar Utilizador**.

    ![Adicionar utilizador](./media/replicon-tutorial/ic777807.png "Adicionar Utilizador")

4. Na secção Perfil do **Utilizador,** execute os seguintes passos:

    ![Perfil de utilizador](./media/replicon-tutorial/ic777808.png "Perfil de utilizador")

    a. Na caixa de texto **'Nome de Login',** digite o endereço de e-mail Azure AD do utilizador Azure AD que pretende providenciar como `B.Simon@contoso.com` .

    > [!NOTE]
    > O Nome de Login precisa de corresponder ao endereço de e-mail do utilizador no AZure AD

    b. Como **Tipo de Autenticação,** selecione **SSO**.

    c. Definir ID de autenticação com o mesmo valor que o Nome de Login (o endereço de e-mail AD Azure do utilizador)

    d. Na caixa de texto do **Departamento,** digite o departamento do utilizador.

    e. Como **Tipo de Empregado,** selecione **Administrador**.

    f. Clique **em Guardar o perfil do utilizador.**

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador replicon ou APIs fornecidas pela Replicon para fornecer contas de utilizador Azure AD.

### <a name="test-sso"></a>Teste SSO

Quando selecionar o azulejo Replicon no Painel de Acesso, deverá ser automaticamente inscrito no Replicon para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)