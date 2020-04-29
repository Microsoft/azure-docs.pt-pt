---
title: 'Tutorial: Integração do Diretório Ativo Azure com a ITRP Microsoft Docs'
description: Neste tutorial, você vai aprender a configurar um único sign-on entre o Azure Ative Directory e o ITRP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e09716a3-4200-4853-9414-2390e6c10d98
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: c4d0f753e23e42b1064fec3f56957dfe97c30ca4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81261198"
---
# <a name="tutorial-azure-active-directory-integration-with-itrp"></a>Tutorial: Integração do Diretório Ativo Azure com a ITRP

Neste tutorial, você vai aprender a integrar itrp com o Azure Ative Directory (Azure AD).
Esta integração proporciona estes benefícios:

* Pode utilizar a AD Azure para controlar quem tem acesso à ITRP.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no ITRP (único sinal) com as suas contas Azure AD.
* Pode gerir as suas contas num local central: o portal Azure.

Para saber mais sobre a integração de aplicações saaS com a Azure AD, consulte [o single sign-on para aplicações no Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o ITRP, é necessário ter:

* Uma subscrição da AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma subscrição ITRP que tem um único sinal ativado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você vai configurar e testar o único sign-on Azure AD em um ambiente de teste.

* Itrp suporta SSO iniciado por SP.

## <a name="add-itrp-from-the-gallery"></a>Adicione ITRP da galeria

Para configurar a integração do ITRP em Azure AD, você precisa adicionar ITRP da galeria à sua lista de aplicações saaS geridas.

1. No [portal Azure,](https://portal.azure.com)no painel esquerdo, selecione **Azure Ative Directory:**

    ![Selecione Azure Active Directory](common/select-azuread.png)

2. Ir a **aplicações** > da Enterprise**Todas as aplicações:**

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

3. Para adicionar uma aplicação, selecione **Nova aplicação** na parte superior da janela:

    ![Selecione Nova aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **itrp**. Selecione **ITRP** nos resultados da pesquisa e, em seguida, **selecione Adicionar**.

     ![Resultados de pesquisa](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, irá configurar e testar um único sinal de Azure AD com a ITRP utilizando uma utilizadora de teste chamada Britta Simon.
Para permitir uma única inscrição, é necessário estabelecer uma relação entre um utilizador da AD Azure e o utilizador correspondente no ITRP.

Para configurar e testar o único sinal de Azure AD com o ITRP, é necessário completar estes passos:

1. Configure o único sinal de entrada **[da AD Azure](#configure-azure-ad-single-sign-on)** para ativar a funcionalidade para os seus utilizadores.
2. **[Configure o let-on único itrp](#configure-itrp-single-sign-on)** no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** para testar o único sinal de AD Azure.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para ativar o único sinal de entrada da Azure AD para o utilizador.
5. **[Crie um utilizador](#create-an-itrp-test-user)** de teste ITRP ligado à representação da AD Azure do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, irá ativar o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único signo da Azure AD com o ITRP, tome estas medidas:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações ITRP, selecione **Single sign-on:**

    ![Selecione um único sinal](common/select-sso.png)

2. Na selectuma única caixa de diálogo do **método de sinalização,** selecione o modo **SAML/WS-Fed** para ativar um único sinal:

    ![Selecione um único método de sinalização](common/select-saml-option.png)

3. Na configuração de um único sign-on com a página **SAML,** selecione o ícone **Editar** para abrir a caixa de diálogo **de configuração SAML básica:**

    ![Ícone editar](common/edit-urls.png)

4. Na caixa de diálogo **Basic SAML Configuration,** dê os seguintes passos.

    ![Caixa de diálogo de configuração SAML básica](common/sp-identifier.png)

    1. No **signo na** caixa URL, introduza um URL neste padrão:
    
       `https://<tenant-name>.itrp.com`

    1. Na caixa **Identifier (Id da Entidade),** introduza um URL neste padrão:

       `https://<tenant-name>.itrp.com`

    > [!NOTE]
    > Estes valores são espaços reservados. Tens de usar o URL e identificador de inscrição real. Contacte a equipa de [apoio itrp](https://www.4me.com/support/) para obter os valores. Também pode consultar os padrões mostrados na caixa de diálogo **Basic SAML Configuration** no portal Azure.

5. Na secção Certificado de **Assinatura SAML,** selecione o ícone **Editar** para abrir a caixa de diálogo do Certificado de **Assinatura SAML:**

    ![Ícone editar](common/edit-certificate.png)

6. Na caixa de diálogo **do Certificado de Assinatura SAML,** copie o valor da impressão **digital** e guarde-o:

    ![Copiar o valor de impressão digital](common/copy-thumbprint.png)

7. Na secção DE Configuração de **ITRP,** copie os URLs apropriados, com base nos seus requisitos:

    ![Copiar os URLs de configuração](common/copy-configuration-urls.png)

    1. **URL de login**.

    1. **Identificador Azure AD**.

    1. **URL de logout**.

### <a name="configure-itrp-single-sign-on"></a>Configure itrp único sign-on

1. Numa nova janela do navegador web, inscreva-se no site da sua empresa ITRP como administrador.

1. Na parte superior da janela, selecione o ícone **Definições:**

    ![Ícone Definições](./media/itrp-tutorial/ic775570.png "Ícone Definições")

1. No painel esquerdo, selecione **Single Sign-On:**

    ![Selecione um único sign-on](./media/itrp-tutorial/ic775571.png "Selecione um único sign-on")

1. Na secção de configuração **de sinal único,** tome os seguintes passos.

    ![Secção de inscrição única](./media/itrp-tutorial/ic775572.png "Secção de inscrição única")

    ![Secção de inscrição única](./media/itrp-tutorial/ic775573.png "Secção de inscrição única")

    1. Selecione **Ativado**.

    1. Na caixa **DEURL de logout remoto,** colhe o valor URL de **Logout** que copiou do portal Azure.

    1. Na caixa **DEURL SAML SSO,** colhe o valor URL de **Login** que copiou do portal Azure.

    1. Na caixa de **impressões digitais** do Certificado, cola o valor **de impressão digital** do certificado, que copiou do portal Azure.

    1. Selecione **Guardar**.

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

Nesta secção, permitirá que Britta Simon use o único sign-on azure, concedendo-lhe acesso à ITRP.

1. No portal Azure, selecione **aplicações Enterprise,** selecione **Todas as aplicações,** e, em seguida, selecione **ITRP**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **ITRP**.

    ![Lista de candidaturas](common/all-applications.png)

3. No painel esquerdo, selecione **Utilizadores e grupos:**

    ![Selecionar Utilizadores e grupos](common/users-groups-blade.png)

4. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** na caixa de diálogo **'Atribuição adicionar'.**

    ![Selecione Adicionar utilizador](common/add-assign-user.png)

5. Na caixa de diálogo **De utilizadores e grupos,** selecione **Britta Simon** na lista de utilizadores e, em seguida, clique no botão **Select** na parte inferior da janela.

6. Se esperar um valor de papel na afirmação Do SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista. Clique no botão **Selecionar** na parte inferior da janela.

7. Na caixa de diálogo **Adicionar Atribuição,** selecione **Atribuir**.

### <a name="create-an-itrp-test-user"></a>Criar um utilizador de teste ITRP

Para permitir que os utilizadores de AD Azure entrem no ITRP, é necessário adicioná-los ao ITRP. Precisa adicioná-las manualmente.

Para criar uma conta de utilizador, tome estas medidas:

1. Inscreva-se no seu inquilino itrp.

1. Na parte superior da janela, selecione o ícone **Registos:**

    ![Ícone de discos](./media/itrp-tutorial/ic775575.png "Ícone de discos")

1. No menu, selecione **Pessoas:**

    ![Selecione Pessoas](./media/itrp-tutorial/ic775587.png "Selecione Pessoas")

1. Selecione o**+** sinal de mais () para adicionar uma nova pessoa:

    ![Selecione o sinal de mais](./media/itrp-tutorial/ic775576.png "Selecione o sinal de mais")

1. Na caixa de diálogo **Adicionar Nova Pessoa,** dê os seguintes passos.

    ![Adicionar caixa de diálogo de nova pessoa](./media/itrp-tutorial/ic775577.png "Adicionar caixa de diálogo de nova pessoa")

    1. Insira o nome e o endereço de e-mail de uma conta Azure AD válida que pretende adicionar.

    1. Selecione **Guardar**.

> [!NOTE]
> Pode utilizar qualquer ferramenta de criação de conta de utilizador ou API fornecida pela ITRP para fornecer contas de utilizador da Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Agora precisa de testar a configuração de um único sinal de acesso do Azure AD utilizando o Painel de Acesso.

Quando selecionar o azulejo ITRP no Painel de Acesso, deve ser automaticamente inscrito na instância ITRP para a qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [O Acesso e utilize aplicações no portal My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Tutoriais para integrar aplicações SaaS no Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
