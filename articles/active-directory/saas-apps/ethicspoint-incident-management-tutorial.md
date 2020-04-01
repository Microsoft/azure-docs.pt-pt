---
title: 'Tutorial: Integração de Diretório Ativo Azure com A Gestão de Incidentes de EthicsPoint (EPIM) [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Diretório Ativo azure e a Gestão de Incidentes de EthicsPoint (EPIM).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8cb31a4c-9309-469b-93ac-daf0d3c7a3e6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 029e45fc4768084b0dcd89b32c5392d26192f7d6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73157151"
---
# <a name="tutorial-azure-active-directory-integration-with-ethicspoint-incident-management-epim"></a>Tutorial: Integração de Diretório Ativo Azure com Gestão de Incidentes de EthicsPoint (EPIM)

Neste tutorial, aprende-se a integrar a EthicsPoint Incident Management (EPIM) com o Azure Ative Directory (Azure AD).
Integrar a Gestão de Incidentes de EthicsPoint (EPIM) com a Azure AD proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso à EthicsPoint Incident Management (EPIM).
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na EthicsPoint Incident Management (EPIM) (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a EthicsPoint Incident Management (EPIM), precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Subscrição ativada por um sinal único de EthicsPoint (EPIM)

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* EthicsPoint Incident Management (EPIM) suporta **SP** iniciado SSO

## <a name="adding-ethicspoint-incident-management-epim-from-the-gallery"></a>Adicionar Gestão de Incidentes de EthicsPoint (EPIM) da galeria

Para configurar a integração da EthicsPoint Incident Management (EPIM) no Azure AD, é necessário adicionar a EthicsPoint Incident Management (EPIM) da galeria à sua lista de aplicações geridas do SaaS.

**Para adicionar a EthicsPoint Incident Management (EPIM) da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite A Gestão de Incidentes de **EthicsPoint (EPIM)**, selecione **EthicsPoint Incident Management (EPIM)** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![Gestão de Incidentes de EthicsPoint (EPIM) na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on azure ad com a EthicsPoint Incident Management (EPIM) com base num utilizador de teste chamado **Britta Simon**.
Para que o início de um único início funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na EthicsPoint Incident Management (EPIM).

Para configurar e testar o único sign-on azure ad com a EthicsPoint Incident Management (EPIM), você precisa completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. Configure a Gestão de Incidentes de **[EthicsPoint (EPIM) Single Sign-On](#configure-ethicspoint-incident-management-epim-single-sign-on)** - para configurar as definições de início de sessão única no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie o utilizador de teste ethicsPoint Incident Management (EPIM)](#create-ethicspoint-incident-management-epim-test-user)** - para ter uma contrapartida da Britta Simon na EthicsPoint Incident Management (EPIM) que esteja ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único signo da Azure AD com a EthicsPoint Incident Management (EPIM), execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **EthicsPoint Incident Management (EPIM),** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Informação de controlo de incidentes de EthicsPoint (EPIM) e URLs única informação de inscrição](common/sp-identifier-reply.png)

    a. Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:
    
    | |
    |--|
    | `https://<companyname>.navexglobal.com`|
    | `https://<companyname>.ethicspointvp.com`|

    b. Na caixa **de identificador,** digite um URL utilizando o seguinte padrão:`https://<companyname>.navexglobal.com/adfs/services/trust`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<servername>.navexglobal.com/adfs/ls/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL, Identificador e Resposta real. Contacte a equipa de suporte ao cliente de [EthicsPoint (EPIM)](https://www.navexglobal.com/company/contact-us) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Federation Metadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

6. Na secção Configurar a Gestão de Incidentes de **EthicsPoint (EPIM),** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de anúncio sinuoso

    c. Logout URL

### <a name="configure-ethicspoint-incident-management-epim-single-sign-on"></a>Configure EthicsPoint Incident Management (EPIM) Single Sign-On

Para configurar um único sign-on no lado de **EthicsPoint Incident Management (EPIM),** você precisa enviar o descarregamento da **Federação Metadados XML** e URLs copiados apropriados do portal Azure para a equipa de suporte de Gestão de Incidentes de [EthicsPoint (EPIM).](https://www.navexglobal.com/company/contact-us) Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No **tipo** de campo de nome utilizador **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso à EthicsPoint Incident Management (EPIM).

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **EthicsPoint Incident Management (EPIM)**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **EthicsPoint Incident Management (EPIM)**.

    ![O link EthicsPoint Incident Management (EPIM) na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-ethicspoint-incident-management-epim-test-user"></a>Criar o utilizador de teste de gestão de incidentes de EthicsPoint (EPIM)

Nesta secção, cria-se um utilizador chamado Britta Simon na EthicsPoint Incident Management (EPIM). Trabalhe com a equipa de suporte de suporte de [EthicsPoint Incident Management (EPIM)](https://www.navexglobal.com/company/contact-us) para adicionar os utilizadores na plataforma EthicsPoint Incident Management (EPIM). Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo EthicsPoint Incident Management (EPIM) no Painel de Acesso, deve ser automaticamente inscrito na EthicsPoint Incident Management (EPIM) para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

