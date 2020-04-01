---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Replicon Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Replicon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 02a62f15-917c-417c-8d80-fe685e3fd601
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b27615b0c76b5c23bbc79788431b0e909b8bf22a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67092772"
---
# <a name="tutorial-integrate-replicon-with-azure-active-directory"></a>Tutorial: Integrar a Replicon com o Diretório Ativo Azure

Neste tutorial, aprenderá a integrar a Replicon com o Azure Ative Directory (Azure AD). Quando integrar a Replicon com a Azure AD, pode:

* Controlo em Azure AD que tem acesso a Replicon.
* Permita que os seus utilizadores sejam automaticamente inscritos na Replicon com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode ter um mês de experiência gratuita [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* A subscrição ativada por um único sinal (SSO) de replicon.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste. A Replicon apoia **o SP** iniciado SSO.

## <a name="adding-replicon-from-the-gallery"></a>Adicionando Replicon da galeria

Para configurar a integração da Replicon em Azure AD, precisa adicionar Replicon da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **da galeria,** digite **Replicon** na caixa de pesquisa.
1. Selecione **Replicon** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Configure e teste Azure AD SSO com Replicon utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Replicon.

Para configurar e testar o Azure AD SSO com replicon, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Replicon SSO](#configure-replicon-sso)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
5. Criar o utilizador do **[teste Replicon](#create-replicon-test-user)** - para ter uma contraparte de B.Simon em Replicon que esteja ligada à representação da AD Azure do utilizador.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração da aplicação **Replicon,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na página **basic SAML Configuração,** introduza os valores para os seguintes campos:

    1. Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://global.replicon.com/!/saml2/<client name>/sp-sso/post`

    1. Na caixa **de identificador,** digite um URL utilizando o seguinte padrão:`https://global.replicon.com/!/saml2/<client name>`

    1. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://global.replicon.com/!/saml2/<client name>/sso/post`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL, Identificador e Resposta real. Contacte a equipa de suporte ao [Cliente da Replicon](https://www.replicon.com/customerzone/contact-support) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Clique no ícone de edição/caneta para o **Certificado de Assinatura SAML** para editar as definições.

    ![Algoritmo de assinatura](common/signing-algorithm.png)

    1. Selecione **A afirmação saml de sinal** como a **opção de assinatura**.

    1. Selecione **SHA-256** como algoritmo de **assinatura**.

1. Na configuração de um único sign-on com a página **SAML,** na secção certificado de **assinatura SAML,** encontre **metadados da Federação XML** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

   ![O link de descarregamento do Certificado](common/metadataxml.png)

### <a name="configure-replicon-sso"></a>Configure Replicon SSO

1. Numa janela diferente do navegador web, inscreva-se no site da sua empresa Replicon como administrador.

2. Para configurar o SAML 2.0, execute os seguintes passos:

    ![Ativar a autenticação SAML](./media/replicon-tutorial/ic777805.png "Ativar a autenticação SAML")

    a. Para visualizar o diálogo **EnableSAML Authentication2,** anexar o seguinte ao seu URL, após a chave da empresa:`/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

    * O seguinte mostra o esquema do URL completo:`https://na2.replicon.com/\<YourCompanyKey\>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

   b. Clique **+** na secção para expandir a secção **v20Configuração.**

   c. Clique **+** na secção para expandir a secção **metaDataConfiguration.**

   d. Selecione **SHA256** para xmlSignatureAlgorithm

   e. Clique **em Escolher Ficheiro,** para selecionar o ficheiro XML do seu fornecedor de identidade e clique em **Submeter**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `BrittaSimon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que b.Simon use o único sign-on Azure, concedendo acesso à Replicon.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Replicon**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

### <a name="create-replicon-test-user"></a>Criar o utilizador do teste de Replicon

O objetivo desta secção é criar um utilizador chamado B.Simon em Replicon.

**Se precisar de criar manualmente o utilizador, execute os seguintes passos:**

1. Numa janela do navegador web, inscreva-se no site da sua empresa Replicon como administrador.

2. Ir para **Utilizadores de Administração \> **.

    ![Utilizadores](./media/replicon-tutorial/ic777806.png "Utilizadores")

3. Clique em **+Adicionar Utilizador**.

    ![Adicionar Utilizador](./media/replicon-tutorial/ic777807.png "Adicionar Utilizador")

4. Na secção perfil do **utilizador,** execute os seguintes passos:

    ![Perfil de utilizador](./media/replicon-tutorial/ic777808.png "Perfil de utilizador")

    a. Na caixa de texto **Sin Name,** digite o endereço de e-mail Azure AD do utilizador Azure AD que pretende fornecer como `B.Simon@contoso.com`.

    > [!NOTE]
    > O Nome de Login precisa de corresponder ao endereço de e-mail do utilizador em Azure AD

    b. Como **Tipo de Autenticação,** selecione **SSO**.

    c. Definir id de autenticação no mesmo valor que o Nome de Login (o endereço de e-mail Azure AD do utilizador)

    d. Na caixa de texto **do Departamento,** digite o departamento do utilizador.

    e. Como **Tipo de Empregado,** selecione **Administrador**.

    f. Clique em **Guardar perfil de utilizador**.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador replicon ou APIs fornecidas pela Replicon para fornecer contas de utilizador Azure AD.

### <a name="test-sso"></a>Teste SSO

Quando selecionar o azulejo Replicon no Painel de Acesso, deve ser automaticamente inscrito no Replicon para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)