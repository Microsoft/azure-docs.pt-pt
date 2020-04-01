---
title: 'Tutorial: Integração de Diretório Sonárgico Azure com a Mercer BenefitsCentral (MBC) [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Mercer BenefitsCentral (MBC).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3788b28c-49aa-4208-9acd-630362008e89
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jeedes
ms.openlocfilehash: 7cc49d41a3ae5777dae87c1730f8119a9e5b58e9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160683"
---
# <a name="tutorial-azure-active-directory-integration-with-mercer-benefitscentral-mbc"></a>Tutorial: Integração de Diretório Ativo Azure com a Mercer BenefitsCentral (MBC)

Neste tutorial, aprende-se a integrar a Mercer BenefitsCentral (MBC) com o Azure Ative Directory (Azure AD).
Integrar a Mercer BenefitsCentral (MBC) com a Azure AD proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso à Mercer BenefitsCentral (MBC).
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Mercer BenefitsCentral (MBC) (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a Mercer BenefitsCentral (MBC), precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Mercer BenefitsCentral (MBC) assinatura única ativada por sinal

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Mercer BenefitsCentral (MBC) suporta **IDP** iniciado SSO

## <a name="adding-mercer-benefitscentral-mbc-from-the-gallery"></a>Adicionando Mercer BenefitsCentral (MBC) da galeria

Para configurar a integração da Mercer BenefitsCentral (MBC) no Azure AD, é necessário adicionar mercer BenefitsCentral (MBC) da galeria à sua lista de aplicações geridas do SaaS.

**Para adicionar mercer BenefitsCentral (MBC) da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Mercer BenefitsCentral (MBC)**, selecione **Mercer BenefitsCentral (MBC)** do painel de resultados e, em seguida, clique em **Adicionar** botão para adicionar a aplicação.

     ![Mercer BenefitsCentral (MBC) na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on azure ad com a Mercer BenefitsCentral (MBC) com base num utilizador de teste chamado **Britta Simon**.
Para que o início de um único início funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na Mercer BenefitsCentral (MBC).

Para configurar e testar o único signo da Azure AD com a Mercer BenefitsCentral (MBC), é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure mercer BenefitsCentral (MBC) Single Sign-On](#configure-mercer-benefitscentral-mbc-single-sign-on)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie o utilizador de teste Mercer BenefitsCentral (MBC)](#create-mercer-benefitscentral-mbc-test-user)** - para ter uma contrapartida da Britta Simon na Mercer BenefitsCentral (MBC) que esteja ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único signo da Azure AD com a Mercer BenefitsCentral (MBC), execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Mercer BenefitsCentral (MBC),** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na configuração de um único sign-on com a página **SAML,** execute os seguintes passos:

    ![Mercer BenefitsCentral (MBC) Domínio e URLs informações únicas de inscrição](common/idp-intiated.png)

    a. Na caixa de texto **identificador,** digite um URL:`stg.mercerhrs.com/saml2.0`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://ssous-stg.mercerhrs.com/SP2/Saml2AssertionConsumer.aspx`

    > [!NOTE]
    > O valor url de resposta não é real. Atualize este valor com o URL de Resposta real. Contacte a equipa de suporte ao [cliente mercer BenefitsCentral (MBC)](https://www.mercer.com/contact-us.html) para obter este valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Federation Metadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

6. Na secção **Configurar mercer BenefitsCentral (MBC),** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de anúncio sinuoso

    c. Logout URL

### <a name="configure-mercer-benefitscentral-mbc-single-sign-on"></a>Configure Mercer BenefitsCentral (MBC) Single Sign-On

Para configurar um único sign-on no lado mercer **BenefitsCentral (MBC),** você precisa enviar o descarregamento da **Federação Metadados XML** e URLs copiados apropriados do portal Azure para a equipa de [suporte Mercer BenefitsCentral (MBC).](https://www.mercer.com/contact-us.html) Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

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

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso à Mercer BenefitsCentral (MBC).

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **Mercer BenefitsCentral (MBC)**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Mercer BenefitsCentral (MBC)**.

    ![O link Mercer BenefitsCentral (MBC) na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-mercer-benefitscentral-mbc-test-user"></a>Criar o utilizador de teste Mercer BenefitsCentral (MBC)

Nesta secção, cria-se uma utilizadora chamada Britta Simon na Mercer BenefitsCentral (MBC). Trabalhe com a equipa de [suporte da Mercer BenefitsCentral (MBC)](https://www.mercer.com/contact-us.html) para adicionar os utilizadores na plataforma Mercer BenefitsCentral (MBC). Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Mercer BenefitsCentral (MBC) no Painel de Acesso, deverá ser automaticamente inscrito no Mercer BenefitsCentral (MBC) para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

