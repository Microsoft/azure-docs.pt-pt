---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Confirmit Horizons Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Confirmit Horizons.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4134ee87-a7df-4328-ba09-4c9a729a29ee
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: 6127f149141db275c4b13c25fd6fbd0241b3e404
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67105039"
---
# <a name="tutorial-azure-active-directory-integration-with-confirmit-horizons"></a>Tutorial: Integração do Diretório Ativo Azure com a Confirmit Horizons

Neste tutorial, aprende-se a integrar a Confirmit Horizons com o Azure Ative Directory (Azure AD).
Integrar a Confirmit Horizons com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Confirmit Horizons.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Confirmit Horizons (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a Confirmit Horizons, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Confirmit Horizons assinatura única ativada por sinal

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Confirmit Horizons apoia **SP e IDP** iniciadoS SSO

* Confirmit Horizons suporta o fornecimento de utilizadores **justo no tempo**

## <a name="adding-confirmit-horizons-from-the-gallery"></a>Adicionando Confirmit Horizons da galeria

Para configurar a integração da Confirmit Horizons em Azure AD, precisa adicionar Confirmit Horizons da galeria à sua lista de aplicações geridas do SaaS.

**Para adicionar Confirmit Horizons da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Confirmit Horizons**, selecione **Confirmit Horizons** a partir do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![Confirmit Horizons na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o single sign-on azure com a Confirmit Horizons com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na Confirmit Horizons.

Para configurar e testar o único sinal de Azure AD com a Confirmit Horizons, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure Confirmit Horizons Single Sign-On](#configure-confirmit-horizons-single-sign-on)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Create Confirmit Horizons test user](#create-confirmit-horizons-test-user)** - para ter uma contrapartida de Britta Simon em Confirmit Horizons que está ligada à representação da AD Azure do utilizador.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure com a Confirmit Horizons, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **confirmit Horizons,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** Se desejar configurar a aplicação no modo iniciado **idp,** execute os seguintes passos:

    ![Confirmit Horizons Domínio e URLs informações únicas de inscrição](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:

    | |
    |--|
    | `https://<SUBDOMAIN>.confirmit.com/identity/AuthServices/<UNIQUEID>` |
    | `https://<SUBDOMAIN>.confirmit.com.au/identity/AuthServices/<UNIQUEID>` |
    | `https://<SUBDOMAIN>.confirmit.ca/identity/AuthServices/<UNIQUEID>` |
    | `https://<SUBDOMAIN>.confirmit.hk/identity/AuthServices/<UNIQUEID>` |
    | `https://sso.us.confirmit.com/<UNIQUEID>` |
    | |

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:

    | |
    |--|
    | `https://<SUBDOMAIN>.confirmit.com/identity/AuthServices/<UNIQUEID>/acs`|
    | `https://<SUBDOMAIN>.confirmit.com.au/identity/AuthServices/<UNIQUEID>/acs`|
    | `https://<SUBDOMAIN>.confirmit.ca/identity/AuthServices/<UNIQUEID>/acs`|
    | `https://<SUBDOMAIN>.confirmit.hk/identity/AuthServices/<UNIQUEID>/acs`|
    | `https://sso.us.confirmit.com/<UNIQUEID>/saml/acs` |
    | |

5. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    ![Confirmit Horizons Domínio e URLs informações únicas de inscrição](common/metadata-upload-additional-signon.png)

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:

    | |
    |--|
    | `https://<SUBDOMAIN>.confirmit.com/identity/<UNIQUEID>` |
    | `https://<SUBDOMAIN>.confirmit.com.au/identity/<UNIQUEID>` |
    | `https://<SUBDOMAIN>.confirmit.ca/identity/<UNIQUEID>` |
    | `https://<SUBDOMAIN>.confirmit.hk/identity/<UNIQUEID>` |
    | `https://sso.us.confirmit.com/<UNIQUEID>` |
    | | 

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação, resposta real e URL de sinalização. Contacte a equipa de suporte ao [cliente confirmit Horizons](mailto:support@confirmit.com) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

4. Na configuração do Single Sign-On com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique no botão de cópia para copiar o Url de **Metadados da Federação** da Aplicação e guarde-o no seu computador.

    ![O link de descarregamento do Certificado](common/copy-metadataurl.png)

### <a name="configure-confirmit-horizons-single-sign-on"></a>Configure Confirmar horizontes único sign-on

Para configurar um único sinal no lado da **Confirmit Horizons,** precisa enviar o Url de **Metadados da Federação de Aplicações** para confirmar a equipa de suporte da [Confirmit Horizons](mailto:support@confirmit.com). Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome,** entre **brittaSimon.**
  
    b. No campo de **nome do utilizador,** escreva**brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso à Confirmit Horizons.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **Confirmit Horizons**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Confirmit Horizons**.

    ![A ligação Confirmit Horizons na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

### <a name="create-confirmit-horizons-test-user"></a>Criar o utilizador de teste Confirmit Horizons

Nesta secção, um utilizador chamado Britta Simon é criado na Confirmit Horizons. A Confirmit Horizons suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir na Confirmit Horizons, um novo é criado após a autenticação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Confirmit Horizons no Painel de Acesso, deverá ser automaticamente inscrito nos Horizontes confirmit para os quais configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

