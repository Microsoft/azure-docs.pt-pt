---
title: 'Tutorial: Integração de Diretório Ativo Azure com o InsideView Microsoft Docs'
description: Neste tutorial, você aprenderá a configurar um único sign-on entre o Azure Ative Directory e o InsideView.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c489a7ab-6b1f-4efb-8a66-8bc13bca78c3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: jeedes
ms.openlocfilehash: 4278f00b1026fe4d1b95634540d6d035d43ca5ab
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82202388"
---
# <a name="tutorial-azure-active-directory-integration-with-insideview"></a>Tutorial: Integração de Diretório Ativo Azure com o InsideView

Neste tutorial, aprenderá a integrar o InsideView com o Azure Ative Directory (Azure AD).
Esta integração proporciona estes benefícios:

* Pode utilizar a AD Azure para controlar quem tem acesso ao InsideView.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no InsideView (único sinal) com as suas contas Azure AD.
* Pode gerir as suas contas num local central: o portal Azure.

Para saber mais sobre a integração de aplicações saaS com a Azure AD, consulte [o single sign-on para aplicações no Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o InsideView, é necessário ter:

* Uma subscrição da AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma subscrição InsideView que tem um único sinal ativado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você vai configurar e testar o único sign-on Azure AD em um ambiente de teste.

* O InsideView suporta o SSO iniciado pelo IDP.

## <a name="add-insideview-from-the-gallery"></a>Adicione InsideView da galeria

Para configurar a integração do InsideView em Azure AD, precisa de adicionar o InsideView da galeria à sua lista de aplicações saaS geridas.

1. No [portal Azure,](https://portal.azure.com)no painel esquerdo, selecione **Azure Ative Directory:**

    ![Selecione Azure Active Directory](common/select-azuread.png)

2. Ir a **aplicações** > da Enterprise**Todas as aplicações:**

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

3. Para adicionar uma aplicação, selecione **Nova aplicação** na parte superior da janela:

    ![Selecione Nova aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **o InsideView**. Selecione **InsideView** nos resultados da pesquisa e, em seguida, **selecione Adicionar**.

    ![Resultados de pesquisa](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, irá configurar e testar um único sinal de Azure AD com o InsideView utilizando uma utilizadora de teste chamada Britta Simon.
Para permitir uma única inscrição, é necessário estabelecer uma relação entre um utilizador da AD Azure e o utilizador correspondente no InsideView.

Para configurar e testar o único sinal de Azure AD com o InsideView, é necessário completar estes passos:

1. Configure o único sinal de entrada **[da AD Azure](#configure-azure-ad-single-sign-on)** para ativar a funcionalidade para os seus utilizadores.
2. **[Configure](#configure-insideview-single-sign-on)** o único sinal do InsideView no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** para testar o único sinal de AD Azure.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para ativar o único sinal de entrada da Azure AD para o utilizador.
5. **[Crie um utilizador](#create-an-insideview-test-user)** de teste InsideView ligado à representação da AD Azure do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, irá ativar o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on da Azure AD com o InsideView, tome estes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações InsideView, selecione **Single sign-on:**

    ![Selecione um único sinal](common/select-sso.png)

2. Na selectuma única caixa de diálogo do **método de sinalização,** selecione o modo **SAML/WS-Fed** para ativar um único sinal:

    ![Selecione um único método de sinalização](common/select-saml-option.png)

3. Na configuração de um único sign-on com a página **SAML,** selecione o ícone **Editar** para abrir a caixa de diálogo **de configuração SAML básica:**

    ![Ícone editar](common/edit-urls.png)

4. Na caixa de diálogo **Basic SAML Configuration,** dê os seguintes passos.

    ![Caixa de diálogo de configuração SAML básica](common/idp-reply.png)

    Na caixa **DEURL resposta,** introduza um URL neste padrão:

    `https://my.insideview.com/iv/<STS Name>/login.iv`

    > [!NOTE]
    > Este valor é um espaço reservado. Precisa usar o URL de resposta real. Contacte a equipa de [suporte InsideView](mailto:support@insideview.com) para obter o valor. Também pode consultar os padrões mostrados na caixa de diálogo **Basic SAML Configuration** no portal Azure.

5. Na **configuração de um único sign-on com** a página SAML, na secção Certificado de **Assinatura SAML,** selecione o link **de descarregamento** ao lado do **Certificado (Raw)**, de acordo com os seus requisitos, e guarde o certificado no seu computador:

    ![Link de descarregamento de certificado](common/certificateraw.png)

6. Na secção Configurar o **InsideView,** copie os URLs apropriados, com base nos seus requisitos:

    ![Copiar os URLs de configuração](common/copy-configuration-urls.png)

    1. **URL de login**.

    1. **Identificador Azure AD**.

    1. **URL de logout**.

### <a name="configure-insideview-single-sign-on"></a>Configure o único sinal do InsideView

1. Numa nova janela do navegador web, inscreva-se no site da sua empresa InsideView como administrador.

1. Na parte superior da janela, selecione **'Administração,** **Definições SingleSignOn'** e, em seguida, **adicione SAML**.
   
   ![Definições de inscrição única SAML](./media/insideview-tutorial/ic794135.png "Definições de inscrição única SAML")

1. Na secção **Adicionar uma nova sAML,** dê os seguintes passos.

    ![Adicione uma nova secção SAML](./media/insideview-tutorial/ic794136.png "Adicione uma nova secção SAML")

    1. Na caixa **de nomeS STS,** introduza um nome para a sua configuração.

    1. Na caixa **SamlP/WS-Fed Unsolicited EndPoint,** colá-cola o valor URL de **Login** que copiou do portal Azure.

    1. Abra o certificado Raw que descarregou do portal Azure. Copie o conteúdo do certificado para a pasta e, em seguida, cole o conteúdo na caixa **de certificado STS.**

    1. Na caixa de mapeamento de **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`** identificação do **utilizador Crm,** introduza .

    1. Na caixa de mapeamento **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`** de **e-mail crm,** introduza .

    1. Na caixa de mapeamento do **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`** primeiro nome **crm,** introduza .

    1. Na caixa **de mapeamento do último nome do Crm,** introduza **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`**.  

    1. Selecione **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar uma utilizadora de teste chamada Britta Simon no portal Azure.

1. No portal Azure, selecione **Azure Ative Directory** no painel esquerdo, selecione **Utilizadores,** e, em seguida, selecione **Todos os utilizadores:**

    ![Selecionar Todos os utilizadores](common/users.png)

2. Selecione **Novo utilizador** na parte superior da janela:

    ![Selecione Novo utilizador](common/new-user.png)

3. Na caixa de diálogo **do Utilizador,** tome os seguintes passos.

    ![Caixa de diálogo do utilizador](common/user-properties.png)

    1. Na caixa **de nomes,** entre **brittaSimon.**
  
    1. Na caixa de **nomes do Utilizador,** introduza **\<BrittaSimon@> de domínio da\< sua empresa.>de extensão. ** (Por exemplo, BrittaSimon@contoso.com.)

    1. Selecione **Mostrar palavra-passe**e, em seguida, anote o valor que está na caixa **password.**

    1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que Britta Simon utilize um único sign-on Azure, concedendo-lhe acesso ao InsideView.

1. No portal Azure, selecione **aplicações Enterprise**, selecione **Todas as aplicações,** e, em seguida, selecione **InsideView**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **InsideView**.

    ![Lista de candidaturas](common/all-applications.png)

3. No painel esquerdo, selecione **Utilizadores e grupos:**

    ![Selecionar Utilizadores e grupos](common/users-groups-blade.png)

4. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** na caixa de diálogo **'Atribuição adicionar'.**

    ![Selecione Adicionar utilizador](common/add-assign-user.png)

5. Na caixa de diálogo **De utilizadores e grupos,** selecione **Britta Simon** na lista de utilizadores e, em seguida, clique no botão **Select** na parte inferior da janela.

6. Se esperar um valor de papel na afirmação Do SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista. Clique no botão **Selecionar** na parte inferior da janela.

7. Na caixa de diálogo **Adicionar Atribuição,** selecione **Atribuir**.

### <a name="create-an-insideview-test-user"></a>Criar um utilizador de teste InsideView

Para permitir que os utilizadores de Anúncios Azure assinem no InsideView, é necessário adicioná-los ao InsideView. Precisa adicioná-las manualmente.

Para criar utilizadores ou contactos no InsideView, contacte a equipa de [suporte InsideView](mailto:support@insideview.com).

> [!NOTE]
> Pode utilizar qualquer ferramenta de criação de conta de utilizador ou API fornecida pelo InsideView para fornecer contas de utilizador da Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Agora precisa de testar a configuração de um único sinal de acesso do Azure AD utilizando o Painel de Acesso.

Quando selecionar o azulejo InsideView no Painel de Acesso, deve ser automaticamente inscrito na instância InsideView para a qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [O Acesso e utilize aplicações no portal My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Tutoriais para integrar aplicações SaaS no Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
