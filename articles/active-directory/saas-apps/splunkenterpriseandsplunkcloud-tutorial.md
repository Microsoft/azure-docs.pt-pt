---
title: 'Tutorial: Integração do Azure Ative Directory com a Splunk Enterprise e a Splunk Cloud Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Splunk Enterprise e a Splunk Cloud.
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
ms.openlocfilehash: 22406fc3ed17e486859fc7e2e2f026619c7c6b55
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88527590"
---
# <a name="tutorial-azure-active-directory-integration-with-splunk-enterprise-and-splunk-cloud"></a>Tutorial: Integração do Azure Ative Directory com a Splunk Enterprise e a Splunk Cloud

Neste tutorial, você aprende a integrar a Splunk Enterprise e a Splunk Cloud com o Azure Ative Directory (Azure AD).
A integração da Splunk Enterprise e da Splunk Cloud com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso à Splunk Enterprise e Splunk Cloud.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Splunk Enterprise e splunk Cloud (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com a Splunk Enterprise e a Splunk Cloud, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Splunk Enterprise e Splunk Cloud única subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Splunk Enterprise e Splunk Cloud suportam **SSO** iniciado SP

## <a name="adding-splunk-enterprise-and-splunk-cloud-from-the-gallery"></a>Adicionando Splunk Enterprise e Splunk Cloud da galeria

Para configurar a integração da Splunk Enterprise e splunk Cloud em Azure AD, você precisa adicionar Splunk Enterprise e Splunk Cloud da galeria à sua lista de aplicações geridas saaS.

**Para adicionar Splunk Enterprise e Splunk Cloud da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **escreva Splunk Enterprise e Splunk Cloud**, selecione **Splunk Enterprise e Splunk Cloud** do painel de resultados e clique em **Adicionar** botão para adicionar a aplicação.

     ![Splunk Enterprise e Splunk Cloud na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD single sign-on com Splunk Enterprise e Splunk Cloud com base em um utilizador de teste chamado **Britta Simon**.
Para um único sign-on para o trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na Splunk Enterprise e splunk Cloud.

Para configurar e testar o Azure AD com a Splunk Enterprise e a Splunk Cloud, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure a Splunk Enterprise e a Splunk Cloud Single Sign-On](#configure-splunk-enterprise-and-splunk-cloud-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Splunk Enterprise e Splunk Cloud test user](#create-splunk-enterprise-and-splunk-cloud-test-user)** - para ter uma contraparte de Britta Simon em Splunk Enterprise e Splunk Cloud que está ligada à representação AD AD Azure do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o único sign-on da Azure AD com a Splunk Enterprise e a Splunk Cloud, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Splunk Enterprise e Splunk Cloud,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Splunk Enterprise e Splunk Cloud Domain e URLs informações únicas de súp livre](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://<splunkserverUrl>/en-US/app/launcher/home`

    b. Na caixa **identifier,** digite um URL utilizando o seguinte padrão: `<splunkserverUrl>`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<splunkserver>/saml/acs`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de Sign-On real, identifier e responder URL. Contacte [a Splunk Enterprise e a splunk Cloud Client support team](https://www.splunk.com/en_us/about-splunk/contact-us.html) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

### <a name="configure-splunk-enterprise-and-splunk-cloud-single-sign-on"></a>Configure Splunk Enterprise e Splunk Cloud Single Sign-On

Para configurar um único sign-on no lado **da Splunk Enterprise e splunk Cloud,** você precisa enviar o **metdata XML da Federação** descarregado e URLs copiados apropriados do portal Azure para a [splunk Enterprise e splunk cloud equipe de suporte](https://www.splunk.com/en_us/about-splunk/contact-us.html). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

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

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso à Splunk Enterprise e Splunk Cloud.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **Splunk Enterprise e Splunk Cloud**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, escreva e selecione **Splunk Enterprise e Splunk Cloud**.

    ![A splunk Enterprise e Splunk Cloud link na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-splunk-enterprise-and-splunk-cloud-test-user"></a>Criar utilizador de teste Splunk Enterprise e Splunk Cloud

Nesta secção, cria-se um utilizador chamado Britta Simon em Splunk Enterprise e Splunk Cloud. Trabalhe com [a equipa de suporte splunk Enterprise e Splunk Cloud](https://www.splunk.com/en_us/about-splunk/contact-us.html) para adicionar os utilizadores na plataforma Splunk Enterprise e Splunk Cloud. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Splunk Enterprise e Splunk Cloud no Painel de Acesso, deverá ser automaticamente inscrito na Splunk Enterprise e Splunk Cloud para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

