---
title: 'Tutorial: Azure Ative Directory integração individual de inscrição (SSO) com o Kit de Ferramentas Azure AD SAML  Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Azure AD AD Toolkit.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3f4348e7-c34e-43c7-926e-f1b26ffacf6d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7902112c1694bacfeb45b5f20db80d5136642169
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77047954"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-azure-ad-saml-toolkit"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com o Kit de Ferramentas Azure AD SAML

Neste tutorial, você vai aprender a integrar o Azure AD AD Toolkit com o Azure Ative Directory (Azure AD). Quando integrar o Kit de Ferramentas Azure AD SAML com a Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Kit de Ferramentas Azure AD SAML.
* Ative que os seus utilizadores sejam automaticamente inscritos no Kit de Ferramentas Azure AD SAML com as suas contas Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura ativada por um único sinal (SSO) do Azure AD Toolkit (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* Kit de ferramentas Azure AD SAML suporta **SP** iniciado SSO
* Assim que configurar o Kit de Ferramentas Azure AD SAML, pode impor o Controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Controlo de Sessão estende-se a partir de Acesso Condicional. [Saiba como impor o controlo de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-azure-ad-saml-toolkit-from-the-gallery"></a>Adicionar kit de ferramentas Azure AD SAML da galeria

Para configurar a integração do Kit de Ferramentas Azure AD SAML no Azure AD, é necessário adicionar o Kit de Ferramentas Azure AD SAML da galeria à sua lista de aplicações geridas saaS.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **de galeria,** digite o Kit de **Ferramentas Azure AD SAML** na caixa de pesquisa.
1. Selecione **Azure AD AD Toolkit** a partir do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-azure-ad-saml-toolkit"></a>Configure e teste azure AD único sinal para Azure AD SAML Toolkit

Configure e teste Azure AD SSO com Kit de Ferramentas Azure AD SAML utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Kit de Ferramentas Azure AD SAML.

Para configurar e testar o Azure AD SSO com o Kit de Ferramentas Azure AD SAML, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure Azure AD SAML Toolkit SSO](#configure-azure-ad-saml-toolkit-sso)** - para configurar as definições de início de sessão individuais no lado da aplicação.
    1. Crie o utilizador de teste de **[Toolkit Azure AD SAML](#create-azure-ad-saml-toolkit-test-user)** - para ter uma contrapartida de B.Simon no Kit de Ferramentas Azure AD SAML que está ligado à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de ferramentas **Azure AD SAML,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na página **basic SAML Configuração,** introduza os valores para os seguintes campos:

    a. No **Sign on URL** text box, escreva um URL: `https://samltoolkit.azurewebsites.net/`

    b. Na caixa de texto **identificador (Id** da entidade), escreva um URL: `https://samltoolkit.azurewebsites.net`

    c. Na caixa de texto **URL resposta,** escreva um URL: `https://samltoolkit.azurewebsites.net/SAML/Consume`

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **certificado (Cru)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de download de certificado](common/certificateraw.png)

1. Na secção **de conjunto azure AD AD Toolkit,** copie os URL(s) adequados com base no seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B. Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **de nome do utilizador,** introduza o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, permitirá que a B.Simon utilize um único sign-on Azure, concedendo acesso ao Kit de Ferramentas Azure AD SAML.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Azure AD SAML Toolkit**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![O link Adicionar usuário](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-azure-ad-saml-toolkit-sso"></a>Configure Azure AD SAML Toolkit SSO

1. Abra uma nova janela do navegador web, caso não tenha registado no website do Azure AD SAML Toolkit, primeiro registe-se clicando no **Registo**. Se já se registou, inscreva-se no site da empresa Azure AD SAML Toolkit utilizando o sinal registado em credenciais.

    ![Registo de Toolkit Azure AD SAML](./media/saml-toolkit-tutorial/register.png)

1. Clique na **configuração SAML**.

    ![Configuração SAML Do kit de ferramentas Azure AD SAML](./media/saml-toolkit-tutorial/saml-configure.png)

1. Clique em **Criar**.

    ![Kit de ferramentas Azure AD SAML Criar SSO](./media/saml-toolkit-tutorial/createsso.png)

1. Na página de **Configuração SAML SSO,** execute os seguintes passos:

    ![Kit de ferramentas Azure AD SAML Criar SSO](./media/saml-toolkit-tutorial/fill-details.png)

    1. Na caixa de texto **login URL,** colá o valor URL de **Login,** que copiou do portal Azure.

    1. Na caixa de texto **Azure AD Identifier,** colá o valor do **Identificador AD Azure,** que copiou do portal Azure.

    1. Na caixa de texto **logout URL,** colá o valor URL de **Logout,** que copiou do portal Azure.

    1. Clique **em Escolher File** e faça upload do ficheiro Certificado **(Raw)** que descarregou a partir do portal Azure.

    1. Clique em **Criar**.

### <a name="create-azure-ad-saml-toolkit-test-user"></a>Criar o utilizador de teste de toolkit Azure AD SAML

Nesta secção, um utilizador chamado B.Simon é criado no Kit de Ferramentas Azure AD SAML. O Kit de Ferramentas Azure AD SAML suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para você nesta seção. Se um utilizador já não existir no Azure AD SAML Toolkit, um novo é criado após a autenticação.

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clicar no azulejo Azure AD SAML Toolkit no Painel de Acesso, deve ser automaticamente inscrito no Kit de Ferramentas Azure AD SAML para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Kit de Ferramentas Azure AD SAML com AD Azure](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger o Kit de Ferramentas Azure AD SAML com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/protect-azure)