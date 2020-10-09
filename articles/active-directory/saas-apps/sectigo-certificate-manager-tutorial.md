---
title: 'Tutorial: Integração do Diretório Ativo do Azure com o Sectigo Certificate Manager Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Sectigo Certificate Manager.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.openlocfilehash: d68e5335fff0341d8808e581061519977e1bb517
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88543283"
---
# <a name="tutorial-azure-active-directory-integration-with-sectigo-certificate-manager"></a>Tutorial: Integração do Diretório Ativo Azure com o Gestor de Certificados sectigo

Neste tutorial, aprende-se a integrar o Sectigo Certificate Manager com o Azure Ative Directory (Azure AD).

Integrar o Sectigo Certificate Manager com a Azure AD dá-lhe os seguintes benefícios:

* Você pode usar Azure AD para controlar quem tem acesso ao Sectigo Certificate Manager.
* Os utilizadores podem ser automaticamente inscritos no Sectigo Certificate Manager com as suas contas AD Azure (súb0 único).
* Pode gerir as suas contas num local central, o portal Azure.

Para obter mais informações sobre software como integração de aplicações de serviço (SaaS) com Azure AD, consulte [um único sinal de acesso às aplicações no Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com o Sectigo Certificate Manager, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição AD Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Assinatura do Sectigo Certificate Manager com um único sinal ativado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa a Azure AD um único sign-on em um ambiente de teste e integrar Sectigo Certificate Manager com Azure AD.

Sectigo Certificate Manager suporta as seguintes funcionalidades:

* **Sinal único iniciado pela SP**
* **Sinal único iniciado pelo IDP**

## <a name="add-sectigo-certificate-manager-in-the-azure-portal"></a>Adicionar Sectigo Certificate Manager no portal Azure

Para integrar o Sectigo Certificate Manager com a Azure AD, tem de adicionar o Sectigo Certificate Manager à sua lista de aplicações geridas pelo SaaS.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No menu esquerdo, selecione **Azure Ative Directory**.

    ![A opção Azure Ative Directory](common/select-azuread.png)

1. Selecione **aplicações da Empresa**  >  **Todas as aplicações**.

    ![O painel de aplicações da Enterprise](common/enterprise-applications.png)

1. Para adicionar uma aplicação, selecione **Nova aplicação**.

    ![A nova opção de aplicação](common/add-new-app.png)

1. Na caixa de pesquisa, **insira o Gestor de Certificados de Sectigo.** Nos resultados da pesquisa, selecione **Sectigo Certificate Manager**e, em seguida, selecione **Add**.

    ![Sectigo Certificate Manager na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD um único sign-on com Sectigo Certificate Manager com base em um utilizador de teste chamado **Britta Simon**. Para um único sinal de sação para o trabalho, deve estabelecer uma relação ligada entre um utilizador AZure AD e o utilizador relacionado no Sectigo Certificate Manager.

Para configurar e testar o Azure AD com o Sectigo Certificate Manager, deve completar os seguintes blocos de construção:

| Tarefa | Descrição |
| --- | --- |
| **[Configurar Azure AD único sinal de inscrição](#configure-azure-ad-single-sign-on)** | Permite que os seus utilizadores utilizem esta funcionalidade. |
| **[Configure Sectigo Certificate Manager único sign-on](#configure-sectigo-certificate-manager-single-sign-on)** | Configura as definições de inscrição única na aplicação. |
| **[Criar um utilizador de teste AZure AD](#create-an-azure-ad-test-user)** | Testa o Azure AD um único sinal para um utilizador chamado Britta Simon. |
| **[Atribuir o utilizador de teste AZure AD](#assign-the-azure-ad-test-user)** | Permite que Britta Simon utilize um único sinal de Azure. |
| **[Criar um utilizador de teste Sectigo Certificate Manager](#create-a-sectigo-certificate-manager-test-user)** | Cria uma contrapartida de Britta Simon em Sectigo Certificate Manager que está ligada à representação AZure AD do utilizador. |
| **[Testar o início de sessão único](#test-single-sign-on)** | Verifica se a configuração funciona. |

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, configurar o Azure AD com o Sectigo Certificate Manager no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)no painel de integração de aplicações **Sectigo Certificate Manager,** selecione **Single sign-on**.

    ![Configurar a opção de inscrição única](common/select-sso.png)

1. No painel **de método de inscrição,** selecione o modo **SAML** ou **SAML/WS-Fed** para ativar um único sinal de súmido.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

1. Na **configuração single Sign-On com painel SAML,** selecione **Editar** (o ícone do lápis) para abrir o painel **de configuração DE SAML básico.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. No painel **de configuração básico do SAML,** para configurar o *modo iniciado pelo IDP,* complete os seguintes passos:

    1. Na caixa **do identificador,** insira um destes URLs:
       * https: \/ /cert-manager.com/shibboleth
       * https: \/ /hard.cert-manager.com/shibboleth

    1. Na caixa **URL de resposta,** insira um destes URLs:
        * https: \/ /cert-manager.com/Shibboleth.sso/SAML2/POST
        * https: \/ /hard.cert-manager.com/Shibboleth.sso/SAML2/POST

    1. Selecione **Definir URLs adicionais**.

    1. Na caixa **de Retransmissão do Estado,** insira um destes URLs:
       * https: \/ /cert-manager.com/customer/SSLSupport/idp
       * https: \/ /hard.cert-manager.com/customer/SSLSupport/idp

    ![Sectigo Certificate Manager domínio e URLs informações únicas de inscrição](common/idp-relay.png)

1.  Para configurar a aplicação no *modo iniciado pelo SP,* complete os seguintes passos:

    * Na **caixa de sinalização na** url, insira um destes URLs:
      * https: \/ /cert-manager.com/Shibboleth.sso/Login
      * https: \/ /hard.cert-manager.com/Shibboleth.sso/Login

      ![Sectigo Certificate Manager domínio e URLs informações únicas de inscrição](common/both-signonurl.png)

1. Na **configuração single Sign-On com painel SAML,** na secção certificado de assinatura **SAML,** selecione **Download** ao lado do **Certificado (Base64)**. Selecione uma opção de descarregamento com base nos seus requisitos. Guarde o certificado no seu computador.

    ![A opção de descarregamento do Certificado (Base64)](common/certificatebase64.png)

1. Na secção **'set up Sectigo Certificate Manager',** copie os seguintes URLs com base nos seus requisitos:

    * URL de Inicio de Sessão
    * Identificador de Azure Ad
    * Logout URL

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-sectigo-certificate-manager-single-sign-on"></a>Configure Sectigo Certificate Manager único sign-on

Para configurar um único sinal no lado do Gestor de Certificados de Sectigo, envie o ficheiro certificado descarregado (Base64) e os URLs relevantes que copiou do portal Azure para a equipa de suporte do [Sectigo Certificate Manager](https://sectigo.com/support). A equipa de apoio do Sectigo Certificate Manager utiliza as informações que lhes envia para garantir que a ligação de sinalização única SAML seja corretamente definida em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

Nesta secção, cria-se um utilizador de teste chamado Britta Simon no portal Azure.

1. No portal Azure, selecione **Utilizadores do Diretório Ativo Azure**  >  **Users**  >  **Todos os utilizadores**.

    ![As opções dos Utilizadores e de Todos os utilizadores](common/users.png)

1. Selecione **Novo utilizador**.

    ![A nova opção de utilizador](common/new-user.png)

1. No painel de **utilização,** complete os seguintes passos:

    1. Na caixa **Nome,** **insira BrittaSimon**.
  
    1. Na caixa **do nome do utilizador,** **introduza brittasimon \@ \<your-company-domain> . \<extension\> ** Por exemplo, ** \@ brittasimon contoso.com**.

    1. Selecione a caixa **de verificação de senha show.** Anota o valor que é apresentado na **caixa de palavra-passe.**

    1. Selecione **Criar**.

    ![O painel de utilizador](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você concede a Britta Simon acesso ao Sectigo Certificate Manager para que ela possa usar a Azure single sign-on.

1. No portal Azure, selecione **aplicações Enterprise**  >  **Todas as aplicações**  >  **Sectigo Certificate Manager**.

    ![O painel de aplicações da Enterprise](common/enterprise-applications.png)

1. Na lista de candidaturas, selecione **Sectigo Certificate Manager**.

    ![Sectigo Certificate Manager na lista de candidaturas](common/all-applications.png)

1. No menu, selecione **Utilizadores e grupos.**

    ![A opção Utilizadores e grupos](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**. Em seguida, no painel de **atribuição Adicionar,** selecione **Utilizadores e grupos**.

    ![O painel de atribuição adicionar](common/add-assign-user.png)

1. No painel **de Utilizadores e grupos,** selecione **Britta Simon** na lista de utilizadores. Escolha **Selecionar**.

1. Se estiver à espera de um valor de função na afirmação SAML, no painel **de funções Select,** selecione a função relevante para o utilizador da lista. Escolha **Selecionar**.

1. No painel **de atribuição de adição,** selecione **Atribuir**.

### <a name="create-a-sectigo-certificate-manager-test-user"></a>Criar um utilizador de teste Sectigo Certificate Manager

Nesta secção, cria-se um utilizador chamado Britta Simon em Sectigo Certificate Manager. Trabalhe com a equipa de apoio do [Sectigo Certificate Manager](https://sectigo.com/support) para adicionar o utilizador na plataforma Sectigo Certificate Manager. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal de Azure AD utilizando o portal My Apps.

Depois de configurar um único s-on, quando selecionar **o Sectigo Certificate Manager** no portal My Apps, é automaticamente inscrito no Sectigo Certificate Manager. Para obter mais informações sobre o portal My Apps, consulte [o Access e utilize aplicações no portal My Apps.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Para saber mais, reveja estes artigos:

- [Lista de tutoriais para integrar apps saaS com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Inscrição única para aplicações no Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


