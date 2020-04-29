---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com a uniFLOW Online [)] integração com a uniFLOW Online [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o uniFLOW Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28313d27-638c-4d50-92ad-d093f2ae9ecf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f26af813fcd4032aabce2305ac8845307d1fca65
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76262134"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-uniflow-online"></a>Tutorial: Azure Ative Diretório integração de um único sign-on (SSO) com a uniFLOW Online

Neste tutorial, você vai aprender a integrar o uniFLOW Online com o Azure Ative Directory (Azure AD). Quando integra o uniFLOW Online com o Azure AD, pode:

* Controlo em Azure AD que tem acesso ao uniFLOW Online.
* Ative que os seus utilizadores entrem no uniFLOW Online com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* uniFLOW Inquilino online.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* uniFLOW Online suporta **SP** iniciado SSO

## <a name="adding-uniflow-online-from-the-gallery"></a>Adicionar uniFLOW Online a partir da galeria

Para configurar a integração do uniFLOW Online em Azure AD, precisa adicionar o uniFLOW Online da galeria à sua lista de aplicações geridas do SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, digite **uniFLOW Online** na caixa de pesquisa.
1. Selecione **uniFLOW Online** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-uniflow-online"></a>Configure e teste Azure AD único signo para uniFLOW Online

Configure e teste Azure AD SSO com uniFLOW Online utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no uniFLOW Online.

Para configurar e testar o Azure AD SSO com o uniFLOW Online, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
   1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
   1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure o uniFLOW Online SSO](#configure-uniflow-online-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Inscreva-se no uniFLOW Online utilizando o utilizador de teste criado](#sign-in-to-uniflow-online-using-the-created-test-user)** - para testar o check-in do utilizador no lado da aplicação.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **uniFLOW Online,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:

    | | |
    |-|-|
    | `https://<tenant_domain_name>.eu.uniflowonline.com`|
    | `https://<tenant_domain_name>.us.uniflowonline.com`|
    | `https://<tenant_domain_name>.sg.uniflowonline.com`|
    | `https://<tenant_domain_name>.jp.uniflowonline.com`|
    | `https://<tenant_domain_name>.au.uniflowonline.com`|

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:

    | | |
    |-|-|
    | `https://<tenant_domain_name>.eu.uniflowonline.com`|
    | `https://<tenant_domain_name>.us.uniflowonline.com`|
    | `https://<tenant_domain_name>.sg.uniflowonline.com`|
    | `https://<tenant_domain_name>.jp.uniflowonline.com`|
    | `https://<tenant_domain_name>.au.uniflowonline.com`|

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e identificador. Contacte a equipa de suporte ao [Cliente Online uniFLOW](mailto:support@nt-ware.com) para obter estes valores. Também pode consultar os padrões mostrados na secção **basic SAML Configuração** no portal Azure ou consultar o URL de resposta apresentado no seu inquilino uniFLOW Online.

1. A aplicação uniFLOW Online espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação uniFLOW Online espera que poucos atributos sejam retransmitidos na resposta SAML que são mostradas abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Nome |  Atributo fonte|
    | -----------| --------------- |
    | nome de exibição | user.displayname |
    | apelido | user.onpremisessamaccountname |

   > [!NOTE]
   > O `user.onpremisessamaccountname` atributo só conterá um valor se os utilizadores de Anúncios Azure estiverem sincronizados a partir de um Diretório Ativo do Windows local.

1. No **set single sign-on com** a página SAML, na secção Certificado de **Assinatura SAML,** clique no botão de cópia para copiar o Url de **Metadados da Federação** da Aplicação e guarde-o no seu computador.

    ![O link de descarregamento do Certificado](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que a B.Simon utilize um único sign-on azure, concedendo acesso ao uniFLOW Online.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **uniFLOW Online**.
1. Na página geral da aplicação, vá à secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

   ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

> [!NOTE]
> Para permitir que todos os utilizadores acedam à aplicação sem atribuição manual, dirija-se à secção **Gerir** e selecione **Propriedades**. Em seguida, altere a atribuição do **utilizador necessária** para o parâmetro PARA **O**.

## <a name="configure-uniflow-online-sso"></a>Configure uniFLOW Online SSO

1. Numa janela diferente do navegador web, inscreva-se no website uniFLOW Online como administrador.

1. A partir do painel de navegação esquerdo, selecione o separador **User.**

    ![configuração online uniFLOW](./media/uniflow-online-tutorial/configure1.png)

1. Clique em **fornecedores de identidade**.

    ![configuração online uniFLOW](./media/uniflow-online-tutorial/configure2.png)

1. Clique em **Adicionar fornecedor**de identidade .

    ![configuração online uniFLOW](./media/uniflow-online-tutorial/configure3.png)

1. Na secção **ADD IDENTITY PROVIDER,** execute os seguintes passos:


    ![configuração online uniFLOW](./media/uniflow-online-tutorial/configure4.png)

    a. Introduza o nome ex: *AzureAD SSO*.

    b. Para **o tipo fornecedor,** selecione a opção **WS-Fed** a partir do dropdown.

    c. Para **o tipo WS-Fed,** selecione a opção **Azure Ative Directory** a partir do dropdown.

    d. Clique em **Guardar**.

1. No separador **Geral,** execute os seguintes passos:

    ![configuração online uniFLOW](./media/uniflow-online-tutorial/configure5.png)

    a. Introduza o nome ex: *AzureAD SSO*.

    b. Selecione a opção **From URL** para os **Metadados da Federação ADFS**.

    c. Na caixa de texto **URl da Federação metadados,** colá o valor de Url de Metadados da Federação de **Aplicações,** que copiou do portal Azure.

    d. Selecione **fornecedor de identidade** como **Ativado**.

    e. Selecione **o registo automático do utilizador** como **Ativado**.

    f. Clique em **Guardar**.

### <a name="sign-in-to-uniflow-online-using-the-created-test-user"></a>Inscreva-se no uniFLOW Online utilizando o utilizador de teste criado

1. Numa janela diferente do navegador web, vá ao URL online uniFLOW para o seu inquilino.

1. Selecione o fornecedor de identidade previamente criado para iniciar sessão através da sua instância AD Azure.

1. Inscreva-se na utilização do utilizador do teste.

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente uniFLOW Online com Azure AD](https://aad.portal.azure.com/)
