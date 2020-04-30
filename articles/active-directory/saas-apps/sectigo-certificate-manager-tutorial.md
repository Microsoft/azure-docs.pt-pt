---
title: 'Tutorial: Integração do Diretório Ativo Azure com O Gestor de Certificados da Sectigo [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Sectigo Certificate Manager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 62cd6987-3373-4b58-b1ff-589f4a3d70a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0447a8dd464363ae7e076dde2520565005d7c0a5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "67588238"
---
# <a name="tutorial-azure-active-directory-integration-with-sectigo-certificate-manager"></a>Tutorial: Integração do Diretório Ativo Azure com O Gestor de Certificados da Sectigo

Neste tutorial, aprende-se a integrar o Gestor de Certificados da Sectigo com o Azure Ative Directory (Azure AD).

Integrar o Gestor de Certificados de Sectigo com a Azure AD dá-lhe os seguintes benefícios:

* Pode utilizar a AD Azure para controlar quem tem acesso ao Gestor de Certificados Da Sectigo.
* Os utilizadores podem ser automaticamente inscritos no Sectigo Certificate Manager com as suas contas Azure AD (única inscrição).
* Pode gerir as suas contas num local central, o portal Azure.

Para obter mais informações sobre software como uma integração de aplicações de serviço (SaaS) com a Azure AD, consulte o [único sign-on para aplicações no Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Gestor de Certificados da Sectigo, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição de AD Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Assinatura do Gestor de Certificados sectigo com um único sinal ativado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o single sign-on Azure AD num ambiente de teste e integra o Gestor de Certificados sectigo com a Azure AD.

O Gestor de Certificados da Sectigo suporta as seguintes funcionalidades:

* **SP-iniciado único sign-on**
* **IDP iniciado único sign-on**

## <a name="add-sectigo-certificate-manager-in-the-azure-portal"></a>Adicione o Gestor de Certificados Sectigo no portal Azure

Para integrar o Gestor de Certificados Sectigo com o Azure AD, deve adicionar o Gestor de Certificados Sectigo à sua lista de aplicações saaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No menu esquerdo, selecione **Azure Ative Directory**.

    ![A opção Azure Ative Diretório](common/select-azuread.png)

1. Selecione **aplicações** > Enterprise**Todas as aplicações**.

    ![O painel de aplicações da Enterprise](common/enterprise-applications.png)

1. Para adicionar uma aplicação, selecione **Nova aplicação**.

    ![A nova opção de candidatura](common/add-new-app.png)

1. Na caixa de pesquisa, insira o **Gestor de Certificados da Sectigo.** Nos resultados da pesquisa, selecione **Sectigo Certificate Manager**, e, em seguida, selecione **Adicionar**.

    ![Gestor de Certificados sectigo na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on do Azure AD com o Sectigo Certificate Manager com base num utilizador de teste chamado **Britta Simon**. Para que o início de sessão simples funcione, deve estabelecer uma relação ligada entre um utilizador da AD Azure e o utilizador relacionado no Sectigo Certificate Manager.

Para configurar e testar o único sign-on azure ad com o Gestor de Certificados da Sectigo, deve completar os seguintes blocos de construção:

| Tarefa | Descrição |
| --- | --- |
| **[Configure Azure AD único sign-on](#configure-azure-ad-single-sign-on)** | Permite que os seus utilizadores utilizem esta funcionalidade. |
| **[Configure Sectigo Certificate Manager único sign-on](#configure-sectigo-certificate-manager-single-sign-on)** | Refigura as definições de inscrição única sintetizadoras na aplicação. |
| **[Criar um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** | Testa o único sinal de ad da Azure para um utilizador chamado Britta Simon. |
| **[Atribuir o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** | Permite que Britta Simon utilize um único sinal de AD Azure. |
| **[Criar um utilizador de teste de Gerente de Certificado satigo](#create-a-sectigo-certificate-manager-test-user)** | Cria uma contrapartida de Britta Simon em Sectigo Certificate Manager que está ligada à representação da AD Azure do utilizador. |
| **[Testar o início de sessão único](#test-single-sign-on)** | Verifica que a configuração funciona. |

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, configura o único sign-on da Azure AD com o Gestor de Certificados Sectigo no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)no painel de integração de aplicações do **Sectigo Certificate Manager,** selecione **Single sign-on**.

    ![Configure uma única opção de inscrição](common/select-sso.png)

1. No select um único painel de método de **início de sinal,** selecione o modo **SAML** ou **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

1. No **set single sign-on com** o painel SAML, selecione **Editar** (o ícone do lápis) para abrir o painel **de configuração SAML básico.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. No painel **de configuração Básica sAML,** para configurar o *modo iniciado pelo IDP,* complete os seguintes passos:

    1. Na caixa **de identificação,** introduza um destes URLs:
       * https:\//cert-manager.com/shibboleth
       * https:\//hard.cert-manager.com/shibboleth

    1. Na caixa **DEURL resposta,** introduza um destes URLs:
        * https:\//cert-manager.com/Shibboleth.sso/SAML2/POST
        * https:\//hard.cert-manager.com/Shibboleth.sso/SAML2/POST

    1. Selecione **Definir URLs adicionais**.

    1. Na caixa **de Retransmissão,** introduza um destes URLs:
       * https:\//cert-manager.com/customer/SSLSupport/idp
       * https:\//hard.cert-manager.com/customer/SSLSupport/idp

    ![Domínio do Gestor de Certificados de Sectigo e informações únicas de inscrição dos URLs](common/idp-relay.png)

1.  Para configurar a aplicação no *modo iniciado por SP,* complete os seguintes passos:

    * Na caixa **de URL Sign on,** introduza um destes URLs:
      * https:\//cert-manager.com/Shibboleth.sso/Login
      * https:\//hard.cert-manager.com/Shibboleth.sso/Login

      ![Domínio do Gestor de Certificados de Sectigo e informações únicas de inscrição dos URLs](common/both-signonurl.png)

1. No **set up Single Sign-On com painel SAML,** na secção Certificado de **Assinatura SAML,** selecione **Download** ao lado do **Certificado (Base64)**. Selecione uma opção de descarregamento com base nos seus requisitos. Guarde o certificado no seu computador.

    ![O certificado (Base64) opção de descarregamento](common/certificatebase64.png)

1. Na secção Configurar O Gestor de **Certificados da Sectigo,** copie os seguintes URLs com base nos seus requisitos:

    * URL de Inicio de Sessão
    * Identificador Azure AD
    * Logout URL

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-sectigo-certificate-manager-single-sign-on"></a>Configure Sectigo Certificate Manager único sign-on

Para configurar um único registo no lado do Gestor de Certificados sectigo, envie o ficheiro Certificado descarregado (Base64) e os URLs relevantes que copiou do portal Azure para a equipa de suporte do Gestor de [Certificados Sectigo.](https://sectigo.com/support) A equipa de suporte do Sectigo Certificate Manager utiliza as informações que envia para garantir que a ligação de inscrição única SAML está corretamente definida em ambos os lados.

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

Nesta secção, você concede a Britta Simon acesso a Sectigo Certificate Manager para que ela possa usar o único sign-on Azure.

1. No portal Azure, selecione **Aplicações** > Enterprise**Todas as aplicações** > **Sectigo Certificate Manager**.

    ![O painel de aplicações da Enterprise](common/enterprise-applications.png)

1. Na lista de candidaturas, selecione **Sectigo Certificate Manager**.

    ![Gestor de Certificados sectigo na lista de candidaturas](common/all-applications.png)

1. No menu, selecione **Utilizadores e grupos**.

    ![A opção Utilizadores e grupos](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**. Em seguida, no painel de **atribuição Adicionar,** selecione **Utilizadores e grupos**.

    ![O painel de atribuição Adicionar](common/add-assign-user.png)

1. No painel **de Utilizadores e grupos,** selecione **Britta Simon** na lista de utilizadores. Escolha **Selecionar**.

1. Se estiver à espera de um valor de papel na afirmação do SAML, no painel de **funções Select,** selecione a função relevante para o utilizador da lista. Escolha **Selecionar**.

1. No painel **adicionar atribuição,** selecione **Atribuir**.

### <a name="create-a-sectigo-certificate-manager-test-user"></a>Criar um utilizador de teste de Gerente de Certificado satigo

Nesta secção, cria-se uma utilizadora chamada Britta Simon em Sectigo Certificate Manager. Trabalhe com a equipa de suporte do [Sectigo Certificate Manager](https://sectigo.com/support) para adicionar o utilizador na plataforma Sectigo Certificate Manager. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal de Acesso AD Azure utilizando o portal My Apps.

Depois de configurar um único sign-on, quando selecionar O Gestor de **Certificados Sectigo** no portal My Apps, é automaticamente inscrito no Sectigo Certificate Manager. Para mais informações sobre o portal My Apps, consulte [O Acesso e utilize aplicações no portal My Apps](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passos seguintes

Para saber mais, reveja estes artigos:

- [Lista de tutoriais para integração de apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Inscrição única para candidaturas no Diretório Ativo do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


