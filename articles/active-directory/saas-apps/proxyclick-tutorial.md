---
title: 'Tutorial: Integração do Diretório Ativo Azure com proxyclick / Microsoft Docs'
description: Neste tutorial, você aprenderá a configurar um único sign-on entre o Azure Ative Directory e proxyclick.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5c58a859-71c2-4542-ae92-e5f16a8e7f18
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 6a4d1c8a390ebd1194d14c057bb32d3111bf39be
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67093498"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>Tutorial: Integração de Diretório Ativo Azure com Proxyclick

Neste tutorial, você vai aprender a integrar proxyclick com o Azure Ative Directory (Azure AD).
Esta integração proporciona estes benefícios:

* Pode utilizar o Azure AD para controlar quem tem acesso a Proxyclick.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Proxyclick (único sinal) com as suas contas Azure AD.
* Pode gerir as suas contas num local central: o portal Azure.

Para saber mais sobre a integração de aplicações saaS com a Azure AD, consulte [o single sign-on para aplicações no Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com proxyclick, é necessário ter:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode [inscrever-se](https://azure.microsoft.com/pricing/free-trial/)para um julgamento de um mês.
* Uma subscrição Proxyclick que tem um único sinal ativado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você vai configurar e testar o único sign-on Azure AD em um ambiente de teste.

* Proxyclick suporta SSO iniciado por SP e idp iniciado.

## <a name="add-proxyclick-from-the-gallery"></a>Adicione Proxyclick da galeria

Para configurar a integração do Proxyclick em Azure AD, você precisa adicionar Proxyclick da galeria à sua lista de aplicações SaaS geridas.

1. No [portal Azure,](https://portal.azure.com)no painel esquerdo, selecione **Azure Ative Directory:**

    ![Selecione Azure Active Directory](common/select-azuread.png)

2. Ir a **aplicações** > da Enterprise**Todas as aplicações:**

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

3. Para adicionar uma aplicação, selecione **Nova aplicação** na parte superior da janela:

    ![Selecione Nova aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **introduza Proxyclick**. Selecione **Proxyclick** nos resultados da pesquisa e, em seguida, **selecione Adicionar**.

     ![Resultados de pesquisa](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, irá configurar e testar um único sign-on azure ad com proxyclick utilizando um utilizador de teste chamado Britta Simon.
Para ativar uma única inscrição, é necessário estabelecer uma relação entre um utilizador da AD Azure e o utilizador correspondente em Proxyclick.

Para configurar e testar o único sinal de Azure AD com proxyclick, é necessário completar estes passos:

1. Configure o único sinal de entrada **[da AD Azure](#configure-azure-ad-single-sign-on)** para ativar a funcionalidade para os seus utilizadores.
2. **[Configure proxyclick único sinal](#configure-proxyclick-single-sign-on)** no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** para testar o único sinal de AD Azure.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para ativar o único sinal de entrada da Azure AD para o utilizador.
5. **[Crie um utilizador](#create-a-proxyclick-test-user)** de teste Proxyclick ligado à representação da AD Azure do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, irá ativar o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure ad com proxyclick, tome estes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações Proxyclick, selecione **Single sign-on:**

    ![Selecione um único sinal](common/select-sso.png)

2. Na selectuma única caixa de diálogo do **método de sinalização,** selecione o modo **SAML/WS-Fed** para ativar um único sinal:

    ![Selecione um único método de sinalização](common/select-saml-option.png)

3. Na configuração de um único sign-on com a página **SAML,** selecione o ícone **Editar** para abrir a caixa de diálogo **de configuração SAML básica:**

    ![Ícone editar](common/edit-urls.png)

4. Na caixa de diálogo **Basic SAML Configuration,** se pretender configurar a aplicação no modo iniciado pelo IDP, tome os seguintes passos.

    ![Caixa de diálogo de configuração SAML básica](common/idp-intiated.png)

    1. Na caixa **de identificador,** introduza um URL neste padrão:
   
       `https://saml.proxyclick.com/init/<companyId>`

    1. Na caixa **DEURL resposta,** introduza um URL neste padrão:

       `https://saml.proxyclick.com/consume/<companyId>`

5. Se pretender configurar a aplicação no modo iniciado por SP, selecione **Definir URLs adicionais**. No **signo na** caixa URL, introduza um URL neste padrão:
   
   `https://saml.proxyclick.com/init/<companyId>`

    ![Proxyclick Domain e URLs informações únicas de inscrição](common/metadata-upload-additional-signon.png)

    

    > [!NOTE]
    > Estes valores são espaços reservados. Tem de utilizar o url do identificador real, resposta e URL de inscrição. Os passos para obter estes valores são descritos mais tarde neste tutorial.

6. Na configuração de um único sinal com a página **SAML,** na secção Certificado de **Assinatura SAML,** selecione o link **de descarregamento** ao lado do **Certificado (Base64)**, de acordo com os seus requisitos, e guarde o certificado no seu computador:

    ![Link de descarregamento de certificado](common/certificatebase64.png)

7. Na secção **Configurar Proxyclick,** copie os URLs apropriados, com base nos seus requisitos:

    ![Copiar os URLs de configuração](common/copy-configuration-urls.png)

    1. **URL de login**.

    1. **Identificador Azure AD**.

    1. **URL de logout**.

### <a name="configure-proxyclick-single-sign-on"></a>Configure Proxyclick único sign-on

1. Numa nova janela do navegador web, inscreva-se no site da sua empresa Proxyclick como administrador.

2. Selecione **Definições de & de Conta**:

    ![Selecione Definições de & de Conta](./media/proxyclick-tutorial/configure1.png)

3. Desloque-se até à secção **integrações** e selecione **SAML:**

    ![Selecione SAML](./media/proxyclick-tutorial/configure2.png)

4. Na secção **SAML,** tome os seguintes passos.

    ![Secção SAML](./media/proxyclick-tutorial/configure3.png)

    1. Copie o valor URL do **Consumidor SAML** e cole-o na caixa URL de **resposta** na caixa de diálogo **Basic SAML Configuration** no portal Azure.

    1. Copie o valor de **URL de redirecionamento SAML SSO** e cole-o no **Sinal nas** caixas URL e **Identifier** na caixa de diálogo **de configuração Básica SAML** no portal Azure.

    1. Na lista de Método de **Pedido SAML,** selecione **HTTP Redirecione**.

    1. Na caixa **Emitente,** reexa o valor do **Identificador AD Azure** que copiou do portal Azure.

    1. Na caixa **DEURL SAML 2.0 Endpoint,** colhe o valor URL de **Login** que copiou do portal Azure.

    1. No Notepad, abra o ficheiro de certificado que descarregou do portal Azure. Colar o conteúdo deste ficheiro na caixa de **certificados.**

    1. Selecione **Guardar Alterações**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar uma utilizadora de teste chamada Britta Simon no portal Azure.

1. No portal Azure, selecione **Azure Ative Directory** no painel esquerdo, selecione **Utilizadores,** e, em seguida, selecione **Todos os utilizadores:**

    ![Selecionar Todos os utilizadores](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã:

    ![Selecione Novo utilizador](common/new-user.png)

3. Na caixa de diálogo **do Utilizador,** tome os seguintes passos.

    ![Caixa de diálogo do utilizador](common/user-properties.png)

    1. Na caixa **de nomes,** entre **brittaSimon.**
  
    1. Na caixa de **nomes do Utilizador,** introduza **\<BrittaSimon@> de domínio da\< sua empresa.>de extensão. ** (Por exemplo, BrittaSimon@contoso.com.)

    1. Selecione **Mostrar palavra-passe**e, em seguida, anote o valor que está na caixa **password.**

    1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que Britta Simon use o único sign-on azure, concedendo-lhe acesso a Proxyclick.

1. No portal Azure, selecione **aplicações Enterprise,** selecione **Todas as aplicações,** e, em seguida, selecione **Proxyclick**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Proxyclick**.

    ![Lista de candidaturas](common/all-applications.png)

3. No painel esquerdo, selecione **Utilizadores e grupos:**

    ![Selecionar Utilizadores e grupos](common/users-groups-blade.png)

4. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** na caixa de diálogo **'Atribuição adicionar'.**

    ![Selecione Adicionar utilizador](common/add-assign-user.png)

5. Na caixa de diálogo **De utilizadores e grupos,** selecione **Britta Simon** na lista de utilizadores e, em seguida, clique no botão **Select** na parte inferior da janela.

6. Se esperar um valor de papel na afirmação Do SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista. Clique no botão **Selecionar** na parte inferior da janela.

7. Na caixa de diálogo **Adicionar Atribuição,** selecione **Atribuir**.

### <a name="create-a-proxyclick-test-user"></a>Criar um utilizador de teste Proxyclick

Para permitir que os utilizadores de Anúncios Azure assinem o Proxyclick, é necessário adicioná-los ao Proxyclick. Precisa adicioná-las manualmente.

Para criar uma conta de utilizador, tome estas medidas:

1. Inscreva-se no site da empresa Proxyclick como administrador.

1. Selecione **Colegas** na parte superior da janela:

    ![Selecionar Colegas](./media/proxyclick-tutorial/user1.png)

1. Selecione **Adicionar Colega:**

    ![Selecione Adicionar Colega](./media/proxyclick-tutorial/user2.png)

1. Na secção **Adicionar um colega,** tome os seguintes passos.

    ![Adicione uma secção de colegas](./media/proxyclick-tutorial/user3.png)

    1. Na caixa de **e-mail,** insira o endereço de e-mail do utilizador. Neste caso, **\@o britânico contoso.com.**

    1. Na caixa **First Name,** introduza o primeiro nome do utilizador. Neste caso, **Britta.**

    1. Na caixa **Sobre Nome,** introduza o último nome do utilizador. Neste caso, **Simon.**

    1. Selecione **Adicionar utilizador**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Agora precisa de testar a configuração de um único sinal de acesso do Azure AD utilizando o Painel de Acesso.

Quando selecionar o azulejo Proxyclick no Painel de Acesso, deve ser automaticamente inscrito na instância Proxyclick para a qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [O Acesso e utilize aplicações no portal My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Tutoriais para integrar aplicações SaaS no Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

