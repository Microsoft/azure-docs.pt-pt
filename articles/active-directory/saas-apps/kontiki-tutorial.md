---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Kontiki Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Kontiki.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8d5e5413-da4c-40d8-b1d0-f03ecfef030b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bfb154bce34b2ceda99b82c7ca3534b8a8ee0a1f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67098493"
---
# <a name="tutorial-azure-active-directory-integration-with-kontiki"></a>Tutorial: Integração do Diretório Ativo Azure com a Kontiki

Neste tutorial, aprende-se a integrar a Kontiki com o Azure Ative Directory (Azure AD).

Integrar a Kontiki com a Azure AD dá-lhe os seguintes benefícios:

* Pode usar o Azure AD para controlar quem tem acesso à Kontiki.
* Os utilizadores podem ser automaticamente inscritos na Kontiki com as suas contas Azure AD (única inscrição).
* Pode gerir as suas contas num local central, o portal Azure.

Para obter mais informações sobre software como uma integração de aplicações de serviço (SaaS) com a Azure AD, consulte o [único sign-on para aplicações no Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a Kontiki, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição de AD Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma subscrição Kontiki com um único sinal ativado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o single sign-on Azure AD num ambiente de teste e integra a Kontiki com o Azure AD.

A Kontiki suporta as seguintes funcionalidades:

* **SP-iniciado único sign-on**
* **Fornecimento de utilizadores just-in-time**

## <a name="add-kontiki-in-the-azure-portal"></a>Adicione Kontiki no portal Azure

Para integrar a Kontiki com o Azure AD, deve adicionar a Kontiki à sua lista de aplicações saaS geridas.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

1. No menu esquerdo, selecione **Azure Ative Directory**.

    ![A opção Azure Ative Diretório](common/select-azuread.png)

1. Selecione **aplicações** > Enterprise**Todas as aplicações**.

    ![O painel de aplicações da Enterprise](common/enterprise-applications.png)

1. Para adicionar uma aplicação, selecione **Nova aplicação**.

    ![A nova opção de candidatura](common/add-new-app.png)

1. Na caixa de pesquisa, entre **kontiki.** Nos resultados da pesquisa, selecione **Kontiki**, e, em seguida, **selecione Adicionar**.

    ![Kontiki na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sign-on azure com a Kontiki com base num utilizador de teste chamado **Britta Simon**. Para que o único início de sessão funcione, deve estabelecer uma relação ligada entre um utilizador da AD Azure e o utilizador relacionado em Kontiki.

Para configurar e testar o único sinal de Azure AD com a Kontiki, deve completar os seguintes blocos de construção:

| Tarefa | Descrição |
| --- | --- |
| **[Configure Azure AD único sign-on](#configure-azure-ad-single-sign-on)** | Permite que os seus utilizadores utilizem esta funcionalidade. |
| **[Configure kontiki único sign-on](#configure-kontiki-single-sign-on)** | Refigura as definições de inscrição única sintetizadoras na aplicação. |
| **[Criar um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** | Testa o único sinal de ad da Azure para um utilizador chamado Britta Simon. |
| **[Atribuir o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** | Permite que Britta Simon utilize um único sinal de AD Azure. |
| **[Criar um utilizador de teste Kontiki](#create-a-kontiki-test-user)** | Cria uma contrapartida de Britta Simon em Kontiki que está ligada à representação da AD Azure do utilizador. |
| **[Testar o início de sessão único](#test-single-sign-on)** | Verifica que a configuração funciona. |

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, configura o único signo da Azure AD com a Kontiki no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)no painel de integração de aplicações **Kontiki,** selecione **Single sign-on**.

    ![Configure uma única opção de inscrição](common/select-sso.png)

1. No select um único painel de método de **início de sinal,** selecione o modo **SAML** ou **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

1. No **set single sign-on com** o painel SAML, selecione **Editar** (o ícone do lápis) para abrir o painel **de configuração SAML básico.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. No painel **de configuração SAML básico,** no **Sign on URL** text box, introduza um URL que tenha o seguinte padrão:`https://<companyname>.mc.eval.kontiki.com`

    ![Kontiki Domain e URLs informações únicas de inscrição](common/sp-signonurl.png)

    > [!NOTE]
    > Contacte a equipa de suporte do [Cliente Kontiki](https://customersupport.kontiki.com/enterprise/contactsupport.html) para obter o valor correto para usar. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. No **set up Single Sign-On com painel SAML,** na secção Certificado de **Assinatura SAML,** selecione **Download** ao lado dos **Metadados da Federação XML**. Selecione uma opção de descarregamento com base nos seus requisitos. Guarde o certificado no seu computador.

    ![A opção de descarregamento de certificados Da Federação Metadados XML](common/metadataxml.png)

1. Na secção **set up Kontiki,** copie os seguintes URLs com base nos seus requisitos:

    * URL de Inicio de Sessão
    * Identificador Azure AD
    * Logout URL

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-kontiki-single-sign-on"></a>Configure kontiki único sign-on

Para configurar um único sinal no lado kontiki, envie o ficheiro XML de Metadados da Federação descarregado e os URLs relevantes que copiou do portal Azure para a equipa de [suporte kontiki.](https://customersupport.kontiki.com/enterprise/contactsupport.html) A equipa de suporte da Kontiki utiliza as informações que envia para garantir que a ligação de assinatura única SAML está corretamente definida em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD 

Nesta secção, cria-se uma utilizadora de teste chamada Britta Simon no portal Azure.

1. No portal Azure, selecione **Utilizadores de Diretório** > **Users** > Ativo Azure**Todos os utilizadores**.

    ![As opções de Utilizadores e Todos os Utilizadores](common/users.png)

1. Selecione **Novo utilizador**.

    ![A nova opção de utilizador](common/new-user.png)

1. No painel **do Utilizador,** complete os seguintes passos:

    1. Na caixa **de nomes,** entre **brittaSimon.**
  
    1. Na caixa de nome sinuosa, **User name** **\@\<introduza\< o seu domínio de empresa>.>de extensão. ** Por exemplo, **contoso.com\@brittasimon.**

    1. Selecione a caixa de verificação de **palavra-passe Mostrar.** Escreva o valor que está exposto na caixa **password.**

    1. Selecione **Criar**.

    ![O painel do Utilizador](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, concedes à Britta Simon acesso à Kontiki para que ela possa usar um único letão azure.

1. No portal Azure, selecione **aplicações** > Enterprise**Todas as aplicações** > **Kontiki**.

    ![O painel de aplicações da Enterprise](common/enterprise-applications.png)

1. Na lista de aplicações, selecione **Kontiki**.

    ![Kontiki na lista de candidaturas](common/all-applications.png)

1. No menu, selecione **Utilizadores e grupos**.

    ![A opção Utilizadores e grupos](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**. Em seguida, no painel de **atribuição Adicionar,** selecione **Utilizadores e grupos**.

    ![O painel de atribuição Adicionar](common/add-assign-user.png)

1. No painel **de Utilizadores e grupos,** selecione **Britta Simon** na lista de utilizadores. Escolha **Selecionar**.

1. Se estiver à espera de um valor de papel na afirmação do SAML, no painel de **funções Select,** selecione a função relevante para o utilizador da lista. Escolha **Selecionar**.

1. No painel **adicionar atribuição,** selecione **Atribuir**.

### <a name="create-a-kontiki-test-user"></a>Criar um utilizador de teste Kontiki

Não há nenhum item de ação para configurar o fornecimento de utilizadores em Kontiki. Quando um utilizador designado tenta iniciar sessão na Kontiki utilizando o portal My Apps, a Kontiki verifica se o utilizador existe. Se não for encontrada nenhuma conta de utilizador, a Kontiki cria automaticamente a conta de utilizador.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal de Acesso AD Azure utilizando o portal My Apps.

Depois de configurar um único sign-on, quando selecionar **kontiki** no portal My Apps, é automaticamente inscrito na Kontiki. Para mais informações sobre o portal My Apps, consulte [O Acesso e utilize aplicações no portal My Apps](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passos seguintes

Para saber mais, reveja estes artigos:

- [Lista de tutoriais para integração de apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Inscrição única para candidaturas no Diretório Ativo do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
