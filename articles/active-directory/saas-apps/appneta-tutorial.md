---
title: 'Tutorial: Azure Ative Directory integração individual (SSO) com appNeta Performance Monitor [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o AppNeta Performance Monitor.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 643a45fb-d6fc-4b32-b721-68899f8c7d44
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ac9e1d32e0280bcf053578aa102cc6fd200a4b2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75561239"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-appneta-performance-monitor"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com o AppNeta Performance Monitor

Neste tutorial, você aprenderá a integrar o AppNeta Performance Monitor com o Azure Ative Directory (Azure AD). Quando integrar o AppNeta Performance Monitor com o Azure AD, pode:

* Controlo em Azure AD que tem acesso ao AppNeta Performance Monitor.
* Ative que os seus utilizadores sejam automaticamente inscritos no AppNeta Performance Monitor com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A subscrição ativada pelo AppNeta Performance Monitor (SSO) permitiu a subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* AppNeta Performance Monitor suporta **SP** iniciado SSO


* AppNeta Performance Monitor suporta o fornecimento de utilizadores **justo no tempo**

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia, pelo que apenas uma instância pode ser configurada num inquilino.


## <a name="adding-appneta-performance-monitor-from-the-gallery"></a>Adicionar appNeta Performance Monitor da galeria

Para configurar a integração do AppNeta Performance Monitor no Azure AD, é necessário adicionar o AppNeta Performance Monitor da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite o **AppNeta Performance Monitor** na caixa de pesquisa.
1. Selecione O Monitor de Desempenho do **AppNeta** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-appneta-performance-monitor"></a>Configure e teste Azure AD único sinal para AppNeta Performance Monitor

Configure e teste Azure AD SSO com AppNeta Performance Monitor utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no AppNeta Performance Monitor.

Para configurar e testar o Azure AD SSO com o AppNeta Performance Monitor, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure o AppNeta Performance Monitor SSO](#configure-appneta-performance-monitor-sso)** - para configurar as definições de início de sessão individuais no lado da aplicação.
    1. **[Crie o utilizador do teste AppNeta Performance Monitor](#create-appneta-performance-monitor-test-user)** - para ter uma contraparte de B.Simon no AppNeta Performance Monitor que está ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **appNeta Performance Monitor,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<subdomain>.pm.appneta.com`

    b. Na caixa de texto **identificador (Id entidade),** escreva um valor:`PingConnect`

    > [!NOTE]
    > O valor do URL de inscrição não é real. Atualize este valor com o URL de Início de Sinal real. Contacte a equipa de suporte ao [Cliente appNeta Performance Monitor](mailto:support@appneta.com) para obter este valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. A aplicação AppNeta Performance Monitor espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/edit-attribute.png)

1. Além de acima, a aplicação AppNeta Performance Monitor espera que poucos atributos sejam retransmitidos na resposta SAML que são mostradas abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com o seu requisito.

    | Nome | Atributo fonte|
    | --------| ----------------|
    | nomePróprio| user.givenname|
    | apelido| utilizador.sobrenome|
    | e-mail| user.userprincipalname|
    | nome| user.userprincipalname|
    | grupos  | user.atribuídos |
    | telefone| user.phonenumber |
    | título| user.jobtitle|
    | | |

    > [!NOTE]
    > **grupos** refere-se ao grupo de segurança em Appneta que é mapeado para um **papel** em Azure AD. Consulte [este](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) doc que explica como criar papéis personalizados em Azure AD.

    1. Clique **Em adicionar nova alegação** para abrir o diálogo de reclamações do utilizador **Gerir.**

    1. Na caixa de texto **Name,** digite o nome do atributo mostrado para essa linha.

    1. Deixe o espaço de **nome em** branco.

    1. Selecione Origem como **Atributo**.

    1. Na lista de **atributos Fonte,** digite o valor do atributo mostrado para essa linha.

    1. Clique em **Ok**

    1. Clique em **Guardar**.

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **metadados da Federação XML** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

1. Na secção Configurar o **AppNeta Performance Monitor,** copie os URL(s) adequados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

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

Nesta secção, permitirá que a B.Simon utilize um único sign-on do Azure, concedendo acesso ao AppNeta Performance Monitor.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **AppNeta Performance Monitor**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-appneta-performance-monitor-sso"></a>Configure AppNeta Performance Monitor SSO

Para configurar um único sinal no lado do **AppNeta Performance Monitor,** é necessário enviar os metadados da **Federação XML** descarregados e URLs copiados apropriados do portal Azure para a equipa de suporte do [AppNeta Performance Monitor](mailto:support@appneta.com). Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

### <a name="create-appneta-performance-monitor-test-user"></a>Criar o utilizador de teste appNeta Performance Monitor

Nesta secção, um utilizador chamado Britta Simon é criado no AppNeta Performance Monitor. O AppNeta Performance Monitor suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador ainda não existir no AppNeta Performance Monitor, um novo é criado após a autenticação.

> [!Note]
> Se precisar de criar um utilizador manualmente, contacte a equipa de suporte do [AppNeta Performance Monitor](mailto:support@appneta.com).

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo do AppNeta Performance Monitor no Painel de Acesso, deve ser automaticamente inscrito no AppNeta Performance Monitor para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o AppNeta Performance Monitor com AD Azure](https://aad.portal.azure.com/)

