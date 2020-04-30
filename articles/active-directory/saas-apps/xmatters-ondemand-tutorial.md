---
title: 'Tutorial: Integração do Diretório Ativo Azure com xMatters OnDemand [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o xMatters OnDemand.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ca0633db-4f95-432e-b3db-0168193b5ce9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: e8ae31122d59238ac104d7d873cf56f32977c9af
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "67086508"
---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Tutorial: Integração do Diretório Ativo Azure com xMatters OnDemand

Neste tutorial, aprende-se a integrar xMatters OnDemand com o Azure Ative Directory (Azure AD).
Integrar xMatters OnDemand com Azure AD proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso a xMatters OnDemand.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no xMatters OnDemand (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com xMatters OnDemand, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* xMatters OnDemand única subscrição ativada por sinal

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* xMatters OnDemand suporta **IDP** iniciado SSO

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>Adicionar xMatters OnDemand da galeria

Para configurar a integração do xMatters OnDemand em Azure AD, precisa adicionar xMatters OnDemand da galeria à sua lista de aplicações saaS geridas.

**Para adicionar xMatters OnDemand da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **xMatters OnDemand**, selecione **xMatters OnDemand** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![xMatters OnDemand na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on azure ad com xMatters OnDemand com base num utilizador de teste chamado **Britta Simon**.
Para que o início de um único início funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no xMatters OnDemand.

Para configurar e testar o único signo azure ad com xMatters OnDemand, você precisa completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure xMatters OnDemand Single Sign-On](#configure-xmatters-ondemand-single-sign-on)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie xMatters OnDemand utilizador](#create-xmatters-ondemand-test-user)** de teste - para ter uma contrapartida de Britta Simon em xMatters OnDemand que esteja ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único signo da Azure AD com xMatters OnDemand, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **xMatters OnDemand,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na configuração de um único sign-on com a página **SAML,** execute os seguintes passos:

    ![xMatters OnDemand Domain e URLs informações únicas de inscrição](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:

    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au/`|
    | `https://<companyname>.cs1.xmatters.com/`|
    | `https://<companyname>.xmatters.com/`|
    | `https://www.xmatters.com`|
    | `https://<companyname>.xmatters.com.au/`|
    | |

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:

    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au`|
    | `https://<companyname>.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.cs1.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.au1.xmatters.com.au/<instancename>`|
    | |

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e resposta real. Contacte a equipa de suporte do [Cliente OnDemand](https://www.xmatters.com/company/contact-us/) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

    > [!IMPORTANT]
    > Precisa encaminhar o certificado para a equipa de [suporte xMatters OnDemand](https://www.xmatters.com/company/contact-us/). O certificado precisa de ser carregado pela equipa de suporte xMatters antes de poder finalizar a configuração de inscrição única.

6. Na secção **Configuração xMatters OnDemand,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-xmatters-ondemand-single-sign-on"></a>Configure xMatters OnDemand Single Sign-On

1. Numa janela diferente do navegador web, inscreva-se no site da sua empresa XMatters OnDemand como administrador.

2. Na barra de ferramentas por cima, clique em **Admin,** e clique em Detalhes da **Empresa** na barra de navegação no lado esquerdo.

    ![Administração](./media/xmatters-ondemand-tutorial/IC776795.png "Administrador")

3. Na página de **Configuração SAML,** execute os seguintes passos:

    ![Configuração SAML](./media/xmatters-ondemand-tutorial/IC776796.png "Configuração SAML")

    a. Selecione **ativar saml**.

    b. Na caixa de texto ID do Fornecedor de **Identidade,** o valor do **identificador AD Azure** que copiou do portal Azure.

    c. No **single Sign On URL** textbox, colhe o valor URL de **Login** que copiou do portal Azure.

    d. Na caixa de texto **single Logout URL,** colhe o **URL de Logout,** que copiou do portal Azure.

    e. Na página detalhes da empresa, no topo, clique em **Guardar Alterações**.

    ![Detalhes da empresa](./media/xmatters-ondemand-tutorial/IC776797.png "Detalhes da empresa")

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No **User name** tipo brittasimon@yourcompanydomain.extensionde campo do nome do utilizador . Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso ao xMatters OnDemand.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **xMatters OnDemand**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **xMatters OnDemand**.

    ![O link xMatters OnDemand na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-xmatters-ondemand-test-user"></a>Criar o utilizador de teste xMatters OnDemand

O objetivo desta secção é criar um utilizador chamado Britta Simon em xMatters OnDemand.

**Se precisar de criar manualmente o utilizador, execute os seguintes passos:**

1. Inscreva-se no seu inquilino **XMatters OnDemand.**

2. Clique no separador **Utilizadores** e, em seguida, clique em **Adicionar utilizador**.

    ![Utilizadores](./media/xmatters-ondemand-tutorial/IC781048.png "Utilizadores")

3. Na secção **Adicionar um Utilizador,** execute os seguintes passos:

    ![Adicionar um Utilizador](./media/xmatters-ondemand-tutorial/IC781049.png "Adicionar um Utilizador")

    a. Selecione **Ative**.

    b. Na caixa de texto ID do **utilizador,** digite o id do utilizador do utilizador como Brittasimon@contoso.com.

    c. Na caixa de texto **First Name,** digite o primeiro nome do utilizador como Britta.

    d. Na caixa de texto **Last Name,** escreva o sobrenome do utilizador como Simon.

    e. Na caixa de texto **do Site,** insira o site válido de uma conta Azure AD válida que pretende fornecer.

    f. Clique em **Guardar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo xMatters OnDemand no Painel de Acesso, deve ser automaticamente inscrito no xMatters OnDemand para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

