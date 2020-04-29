---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com a Sonarqube [ Sonarqube] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Sonarqube.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b01665e7-c3d0-4393-9286-d5bcf8cf6add
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 571d8849fd7cae5c872a56182858848dbb43ef42
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "72026682"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sonarqube"></a>Tutorial: Azure Ative Directory integração individual (SSO) com a Sonarqube

Neste tutorial, você vai aprender a integrar sonarqube com o Azure Ative Directory (Azure AD). Quando integrar o Sonarqube com a Azure AD, pode:

* Controlo em Azure AD que tem acesso a Sonarqube.
* Permita que os seus utilizadores sejam automaticamente inscritos na Sonarqube com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Sonarqube single sign-on (SSO) enabled subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Sonarqube apoia **SP** iniciado SSO

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia, pelo que apenas uma instância pode ser configurada num inquilino.

## <a name="adding-sonarqube-from-the-gallery"></a>Adicionando Sonarqube da galeria

Para configurar a integração da Sonarqube em Azure AD, você precisa adicionar Sonarqube da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **da galeria,** **digite Sonarqube** na caixa de pesquisa.
1. **Selecione Sonarqube** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sonarqube"></a>Configure e teste Azure AD único sign-on para Sonarqube

Configure e teste Azure AD SSO com Sonarqube utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Sonarqube.

Para configurar e testar o Azure AD SSO com o Sonarqube, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure o Sonarqube SSO](#configure-sonarqube-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Crie um utilizador de teste Sonarqube](#create-sonarqube-test-user)** - para ter uma contrapartida de B.Simon em Sonarqube que esteja ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **da Sonarqube,** encontre a secção **Gerir** e selecione um **único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    Na caixa de texto **de URL sign-on,** escreva um URL:

    * **Para o Ambiente de Produção**

        `https://servicessonar.corp.microsoft.com/`

    * **Para Dev Ambiente**

        `https://servicescode-dev.westus.cloudapp.azure.com`

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção **Configurar sonarqube,** copie os URL(s) adequados com base na sua exigência.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que b.Simon use o único sign-on Azure, concedendo acesso ao Sonarqube.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Sonarqube**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-sonarqube-sso"></a>Configure Sonarqube SSO

1. Abra uma nova janela do navegador web e inscreva-se no site da sua empresa Sonarqube como administrador.

2. Instale o plugin SAML a partir do mercado de sonarqube.

3. Na parte superior à esquerda da página, clique na **ADMIN** e depois navegue para **saml**.

4. Na página **SAML,** execute os seguintes passos:

    ![Configuração sonarqube](./media/sonarqube-tutorial/config01.png)

    a. Alternar a opção **Ativada** para **sim**.

    b. Na caixa de texto de id de **aplicação,** introduza o nome como **sonarqube**.

    c. Na caixa de texto **'Nome** fornecedora', introduza o nome **saml**.

    d. Na caixa de texto **ID do Fornecedor,** colhe o valor do **Identificador Azure AD,** que copiou do portal Azure.

    e. Na caixa de texto de url de **login SAML,** colá o valor do URL de **Login,** que copiou do portal Azure.

    f. Abra o certificado codificado Base64 no bloco de notas, copie o seu conteúdo e cole-o na caixa de texto do **certificado fornecedor.**

    g. Na caixa de texto de atributo de `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`login do **utilizador SAML,** introduza o valor .

    h. Na caixa de texto de atribuição de `http://schemas.microsoft.com/identity/claims/displayname`nome de **utilizador SAML,** introduza o valor .

    i. Na caixa de texto de atributo de `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` **utilizador SAML,** introduza o valor .

    j. Clique em **Guardar**.

### <a name="create-sonarqube-test-user"></a>Criar o utilizador de teste Sonarqube

Nesta secção, cria-se um utilizador chamado B.Simon em Sonarqube. Trabalhe com a equipa de suporte ao [Cliente Sonarqube](https://www.sonarsource.com/support/) para adicionar os utilizadores na plataforma Sonarqube. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal. 

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Sonarqube no Painel de Acesso, deve ser automaticamente inscrito no Sonarqube para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente Sonarqube com Azure AD](https://aad.portal.azure.com/)

