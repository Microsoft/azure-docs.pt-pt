---
title: 'Tutorial: Integração de Diretório Ativo Azure com o HubSpot Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o HubSpot.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 57343ccd-53ea-4e62-9e54-dee2a9562ed5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c4b235426a7029abb9bb79ba56e582cccc3b14a6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "68944452"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>Tutorial: Integração de Diretório Ativo Azure com o HubSpot

Neste tutorial, aprende-se a integrar o HubSpot com o Azure Ative Directory (Azure AD).

Integrar o HubSpot com o Azure AD dá-lhe os seguintes benefícios:

* Pode utilizar o Azure AD para controlar quem tem acesso ao HubSpot.
* Os utilizadores podem ser automaticamente inscritos no HubSpot com as suas contas Azure AD (única sessão).
* Pode gerir as suas contas num local central, o portal Azure.

Para obter mais informações sobre software como uma integração de aplicações de serviço (SaaS) com a Azure AD, consulte o [único sign-on para aplicações no Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o HubSpot, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição de AD Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma subscrição HubSpot com um único sinal ativado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sign-on azure num ambiente de teste e integra o HubSpot com o Azure AD.

O HubSpot suporta as seguintes funcionalidades:

* **SP-iniciado único sign-on**
* **IDP iniciado único sign-on**

## <a name="add-hubspot-in-the-azure-portal"></a>Adicione hubSpot no portal Azure

Para integrar o HubSpot com o Azure AD, tem de adicionar o HubSpot à sua lista de aplicações SaaS geridas.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

1. No menu esquerdo, selecione **Azure Ative Directory**.

    ![A opção Azure Ative Diretório](common/select-azuread.png)

1. Selecione **aplicações** > Enterprise**Todas as aplicações**.

    ![O painel de aplicações da Enterprise](common/enterprise-applications.png)

1. Para adicionar uma aplicação, selecione **Nova aplicação**.

    ![A nova opção de candidatura](common/add-new-app.png)

1. Na caixa de pesquisa, introduza o **HubSpot**. Nos resultados da pesquisa, selecione **HubSpot**, e, em seguida, **selecione Adicionar**.

    ![HubSpot na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sign-on azure com hubSpot com base num utilizador de teste chamado **Britta Simon**. Para que o início de sessão seja introduzido, deve estabelecer uma relação ligada entre um utilizador da AD Azure e o utilizador relacionado no HubSpot.

Para configurar e testar o único sinal de Azure AD com o HubSpot, deve completar os seguintes blocos de construção:

| Tarefa | Descrição |
| --- | --- |
| **[Configure Azure AD único sign-on](#configure-azure-ad-single-sign-on)** | Permite que os seus utilizadores utilizem esta funcionalidade. |
| **[Configure hubSpot único sign-on](#configure-hubspot-single-sign-on)** | Refigura as definições de inscrição única sintetizadoras na aplicação. |
| **[Criar um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** | Testa o único sinal de ad da Azure para um utilizador chamado Britta Simon. |
| **[Atribuir o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** | Permite que Britta Simon utilize um único sinal de AD Azure. |
| **[Criar um utilizador de teste HubSpot](#create-a-hubspot-test-user)** | Cria uma contrapartida de Britta Simon no HubSpot que está ligada à representação da AD Azure do utilizador. |
| **[Testar o início de sessão único](#test-single-sign-on)** | Verifica que a configuração funciona. |

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, configura o único sign-on azure ad com o HubSpot no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)no painel de integração de aplicações **HubSpot,** selecione **Single sign-on**.

    ![Configure uma única opção de inscrição](common/select-sso.png)

1. No select um único painel de método de **início de sinal,** selecione o modo **SAML** ou **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

1. No **set single sign-on com** o painel SAML, selecione **Editar** (o ícone do lápis) para abrir o painel **de configuração SAML básico.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. No painel **de configuração Básica sAML,** para configurar o *modo iniciado pelo IDP,* complete os seguintes passos:

    1. Na caixa **Identificador,** introduza um URL com\/o seguinte\<padrão: https: /api.hubspot.com/login-api/v1/saml/login?portalId= ID DO\>CLIENTE .

    1. Na caixa **DEURL resposta,** introduza um URL\/com o\<seguinte\>padrão: https: /api.hubspot.com/login-api/v1/saml/acs?portalId= ID DO CLIENTE .

    ![Informação de assinatura única do domínio HubSpot e URLs](common/idp-intiated.png)

    > [!NOTE]
    > Para formatar os URLs, também pode consultar os padrões mostrados no painel **de configuração SAML Básico** no portal Azure.

1. Para configurar a aplicação no modo *iniciado por SP:*

    1. Selecione **Definir URLs adicionais**.

    1. No **caixa de URL Sign on,** **introduza\/https: /app.hubspot.com/login**.

    ![A opção Deset adicionais URLs](common/metadata-upload-additional-signon.png)

1. No **set up Single Sign-On com painel SAML,** na secção Certificado de **Assinatura SAML,** selecione **Download** ao lado do **Certificado (Base64)**. Selecione uma opção de descarregamento com base nos seus requisitos. Guarde o certificado no seu computador.

    ![O certificado (Base64) opção de descarregamento](common/certificatebase64.png)

1. Na secção Configurar o **HubSpot,** copie os seguintes URLs com base nos seus requisitos:

    * URL de Inicio de Sessão
    * Identificador Azure AD
    * Logout URL

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-hubspot-single-sign-on"></a>Configure hubSpot único sign-on

1. Abra um novo separador no seu navegador e inscreva-se na sua conta de administrador do HubSpot.

1. Selecione o ícone **Definições** no canto superior direito da página.

    ![O ícone de Definições no HubSpot](./media/hubspot-tutorial/config1.png)

1. Selecione **Predefinições de Conta**.

    ![A opção Predefinição da Conta no HubSpot](./media/hubspot-tutorial/config2.png)

1. Desloque-se até à secção **de Segurança** e, em seguida, selecione **Configurar**.

    ![A opção Configurar no HubSpot](./media/hubspot-tutorial/config3.png)

1. Na secção **de inscrição única,** complete os seguintes passos:

    1. Na caixa **URl (Entidade fornecedora de serviços)** selecione **Copy** para copiar o valor. No portal Azure, no painel **de configuração SAML Básico,** cola o valor na caixa **de identificação.**

    1. No **Sign on URl, ACS, Destinatário ou caixa de redirecionamento,** selecione **Copy** para copiar o valor. No portal Azure, no painel **de configuração SAML Básico,** cola o valor na caixa URL de **resposta.**

    1. No HubSpot, na caixa de URL do **Identificador de Fornecedor de Identidade ou emitente,** colhe o valor para **o Identificador AD Azure** que copiou no portal Azure.

    1. No HubSpot, na caixa **de URL single sign-on** do fornecedor de identidade, colá-cola o valor para **URL de login** que copiou no portal Azure.

    1. No Windows Notepad, abra o ficheiro Certificate(Base64) que descarregou. Selecione e copie o conteúdo do ficheiro. Em seguida, no HubSpot, cola-o na caixa de **certificadoX.509.**

    1. Selecione **Verificar**.

        ![A secção de inscrição única configurada no HubSpot](./media/hubspot-tutorial/config4.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, cria-se uma utilizadora de teste chamada Britta Simon no portal Azure.

1. No portal Azure, selecione **Utilizadores de Diretório** > **Users** > Ativo Azure**Todos os utilizadores**.

    ![As opções de Utilizadores e Todos os Utilizadores](common/users.png)

1. Selecione **Novo utilizador**.

    ![A nova opção de utilizador](common/new-user.png)

1. No painel **do Utilizador,** complete os seguintes passos:

    1. Na caixa **de nomes,** entre **brittaSimon.**
  
    1. Na caixa de nome sinuosa, **User name** **\@\<introduza\< o seu domínio de empresa>. extensão\>**. Por exemplo, **contoso.com\@brittasimon.**

    1. Selecione a caixa de verificação de **palavra-passe Mostrar.** Escreva o valor que está exposto na caixa **password.**

    1. Selecione **Criar**.

    ![O painel do Utilizador](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, você concede a Britta Simon acesso ao HubSpot para que ela possa usar o único sign-on Azure.

1. No portal Azure, selecione **aplicações** > Enterprise**Todas as aplicações** > **HubSpot**.

    ![O painel de aplicações da Enterprise](common/enterprise-applications.png)

1. Na lista de aplicações, selecione **HubSpot**.

    ![HubSpot na lista de aplicações](common/all-applications.png)

1. No menu, selecione **Utilizadores e grupos**.

    ![A opção Utilizadores e grupos](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**. Em seguida, no painel de **atribuição Adicionar,** selecione **Utilizadores e grupos**.

    ![O painel de atribuição Adicionar](common/add-assign-user.png)

1. No painel **de Utilizadores e grupos,** selecione **Britta Simon** na lista de utilizadores. Escolha **Selecionar**.

1. Se estiver à espera de um valor de papel na afirmação do SAML, no painel de **funções Select,** selecione a função relevante para o utilizador da lista. Escolha **Selecionar**.

1. No painel **adicionar atribuição,** selecione **Atribuir**.

### <a name="create-a-hubspot-test-user"></a>Criar um utilizador de teste HubSpot

Para permitir que o Azure AD um utilizador inscreva no HubSpot, o utilizador deve ser aprovisionado no HubSpot. No HubSpot, o provisionamento é uma tarefa manual.

Para fornecer uma conta de utilizador no HubSpot:

1. Inscreva-se no site da empresa HubSpot como administrador.

1. Selecione o ícone **Definições** no canto superior direito da página.

    ![O ícone de Definições no HubSpot](./media/hubspot-tutorial/config1.png)

1. Selecione **Utilizadores & Equipas**.

    ![A opção Users & Teams no HubSpot](./media/hubspot-tutorial/user1.png)

1. Selecione **Criar o utilizador**.

    ![A opção criar o utilizador no HubSpot](./media/hubspot-tutorial/user2.png)

1. Na caixa **de addess(es)** do email Add, introduza o\@endereço de e-mail do utilizador no formato brittasimon contoso.com e, em seguida, selecione **Next**.

    ![Caixa de endereços de e-mail (es) adicionar na secção Criar utilizadores no HubSpot](./media/hubspot-tutorial/user3.png)

1. Na secção **Criar utilizadores,** selecione cada separador. Em cada separador, detete as opções e permissões relevantes para o utilizador. Em seguida, selecione **Next**.

    ![Separadores na secção Criar utilizadores no HubSpot](./media/hubspot-tutorial/user4.png)

1. Para enviar o convite ao utilizador, selecione **Enviar**.

    ![A opção Enviar no HubSpot](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > O utilizador é ativado após o utilizador aceitar o convite.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal de Acesso AD Azure utilizando o portal My Apps.

Depois de configurar um único sign-on, quando selecionar o **HubSpot** no portal My Apps, está automaticamente inscrito no HubSpot. Para mais informações sobre o portal My Apps, consulte [O Acesso e utilize aplicações no portal My Apps](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passos seguintes

Para saber mais, reveja estes artigos:

- [Lista de tutoriais para integração de apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Inscrição única para candidaturas no Diretório Ativo do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
