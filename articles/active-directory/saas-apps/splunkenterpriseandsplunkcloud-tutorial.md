---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Splunk Enterprise e a Splunk Cloud [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Splunk Enterprise e a Splunk Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: b3e2b4a9-749c-4895-813d-db46f8dfdbf8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb0941a6cdde5b60b71da6f58ea6b0fd2ccbb262
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160957"
---
# <a name="tutorial-azure-active-directory-integration-with-splunk-enterprise-and-splunk-cloud"></a>Tutorial: Integração de Diretório Ativo Azure com a Splunk Enterprise e a Splunk Cloud

Neste tutorial, aprende-se a integrar a Splunk Enterprise e a Splunk Cloud com o Azure Ative Directory (Azure AD).
Integrar a Splunk Enterprise e a Splunk Cloud com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso à Splunk Enterprise e Splunk Cloud.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Splunk Enterprise e splunk Cloud (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da Azure AD com a Splunk Enterprise e a Splunk Cloud, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Splunk Enterprise e Splunk Cloud única subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Splunk Enterprise e Splunk Cloud **suportaS SP** iniciado SSO

## <a name="adding-splunk-enterprise-and-splunk-cloud-from-the-gallery"></a>Adicionando A Splunk Enterprise e Splunk Cloud da galeria

Para configurar a integração da Splunk Enterprise e da Splunk Cloud em Azure AD, você precisa adicionar Splunk Enterprise e Splunk Cloud da galeria para a sua lista de aplicações geridas saaS.

**Para adicionar A Splunk Enterprise e Splunk Cloud da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **A Empresa Splunk e splunk Cloud,** selecione **Splunk Enterprise e Splunk Cloud** do painel de resultados e, em seguida, clique em **Adicionar** botão para adicionar a aplicação.

     ![Splunk Enterprise e Splunk Cloud na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on da Azure AD com a Splunk Enterprise e a Splunk Cloud com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na Splunk Enterprise e na Splunk Cloud.

Para configurar e testar o único sign-on da Azure AD com a Splunk Enterprise e a Splunk Cloud, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure a Splunk Enterprise e o Splunk Cloud Single Sign-On](#configure-splunk-enterprise-and-splunk-cloud-single-sign-on)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. Crie o utilizador de **[testes Splunk Enterprise e Splunk Cloud](#create-splunk-enterprise-and-splunk-cloud-test-user)** - para ter uma contrapartida da Britta Simon na Splunk Enterprise e splunk Cloud que está ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on da Azure AD com a Splunk Enterprise e a Splunk Cloud, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **da Splunk Enterprise e Splunk Cloud,** selecione **single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Splunk Enterprise e Splunk Cloud Domain e URLs informações únicas de inscrição](common/sp-identifier-reply.png)

    a. Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<splunkserverUrl>/en-US/app/launcher/home`

    b. Na caixa **de identificador,** digite um URL utilizando o seguinte padrão:`<splunkserverUrl>`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<splunkserver>/saml/acs`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL, Identificador e Resposta real. Contacte a equipa de suporte ao [Cliente Splunk Enterprise e Splunk Cloud](https://www.splunk.com/about-us/contact.html#tabs/customer-support) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Federation Metadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

### <a name="configure-splunk-enterprise-and-splunk-cloud-single-sign-on"></a>Configure Splunk Enterprise e Splunk Cloud Single Sign-On

Para configurar um único sign-on no **lado da Splunk Enterprise e da Splunk Cloud,** você precisa enviar os metadados da **Federação xML** descarregados e URLs copiados apropriados do portal Azure para [splunk Enterprise e splunk cloud support team](https://www.splunk.com/about-us/contact.html#tabs/customer-support). Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

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

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso à Splunk Enterprise e à Splunk Cloud.

1. No portal Azure, selecione **Aplicações Empresariais**, selecione **Todas as aplicações,** em seguida, selecione **Splunk Enterprise e Splunk Cloud**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, digite e selecione **Splunk Enterprise e Splunk Cloud**.

    ![O link Splunk Enterprise e Splunk Cloud na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-splunk-enterprise-and-splunk-cloud-test-user"></a>Crie o utilizador de teste Splunk Enterprise e Splunk Cloud

Nesta secção, cria-se um utilizador chamado Britta Simon na Splunk Enterprise e na Splunk Cloud. Trabalhe com a equipa de [suporte da Splunk Enterprise e da Splunk Cloud](https://www.splunk.com/about-us/contact.html#tabs/customer-support) para adicionar os utilizadores na plataforma Splunk Enterprise e Splunk Cloud. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Splunk Enterprise e Splunk Cloud no Painel de Acesso, deve ser automaticamente inscrito na Splunk Enterprise e splunk Cloud para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

