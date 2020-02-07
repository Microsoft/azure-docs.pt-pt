---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com a EAB Navigate IMPL  Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o EAB Navigate IMPL.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 51460c55-2239-49b6-8a5e-7c8baacad518
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 465b41aaf3c3b16dcba489d1ea9ba951a3108c8e
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77046589"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-eab-navigate-impl"></a>Tutorial: Azure Ative Diretório integração de um único sign-on (SSO) com a EAB Navigate IMPL

Neste tutorial, você vai aprender a integrar o EAB Navigate IMPL com o Azure Ative Directory (Azure AD). Quando integrar o EAB Navigate IMPL com a Azure AD, pode:

* Controlo em Azure AD que tem acesso ao EAB Navigate IMPL.
* Ative que os seus utilizadores sejam automaticamente inscritos no EAB Navigate IMPL com as suas contas Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* EAB Navigate IMPL single sign-on (SSO) enabled subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* EAB Navigate IMPL suporta **SP** iniciado SSO

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo, de modo que apenas uma instância pode ser configurada em um locatário.

## <a name="adding-eab-navigate-impl-from-the-gallery"></a>Adicionar eAB Navigate IMPL da galeria

Para configurar a integração do EAB Navigate IMPL em Azure AD, você precisa adicionar EAB Navigate IMPL da galeria à sua lista de aplicações saaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, digite **EAB Navigate IMPL** na caixa de pesquisa.
1. Selecione **EAB Navigate IMPL** a partir do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-eab-navigate-impl"></a>Configure e teste Azure AD único sign-on para EAB Navigate IMPL

Configure e teste Azure AD SSO com EAB Navigate IMPL utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no EAB Navigate IMPL.

Para configurar e testar o Azure AD SSO com o EAB Navigate IMPL, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure o EAB Navigate IMPL SSO](#configure-eab-navigate-impl-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Crie o utilizador de teste EAB Navigate IMPL](#create-eab-navigate-impl-test-user)** - para ter uma contrapartida de B.Simon no EAB Navigate IMPL que esteja ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **EAB Navigate IMPL,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos: Na caixa de texto **identificador (Id da entidade),** introduza exatamente o seguinte valor: `https://impl.bouncer.eab.com`
    
    Na caixa de texto URL de **resposta (URL do Serviço de Consumidor de Afirmação),** introduza os seguintes valores como linhas separadas: `https://impl.bouncer.eab.com/sso/saml2/acs`
    `https://impl.bouncer.eab.com/sso/saml2/acs/`
    
    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.navigate.impl.eab.com/`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de logon real. Contacte a equipa de suporte ao [cliente EAB Navigate IMPL](mailto:EABTechSupport@eab.com) para obter o valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. No **set single sign-on com** a página SAML, na secção Certificado de **Assinatura SAML,** clique no botão de cópia para copiar o Url de **Metadados da Federação** da Aplicação e guarde-o no seu computador.

    ![O link de download de certificado](common/copy-metadataurl.png)

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

Nesta secção, permitirá que a B.Simon utilize um único sign-on Azure, concedendo acesso ao EAB Navigate IMPL.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **EAB Navigate IMPL**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![O link Adicionar usuário](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-eab-navigate-impl-sso"></a>Configure EAB Navigate IMPL SSO

Para configurar um único sinal no lado do IMPL de navegação da **EAB,** precisa enviar o Url de **Metadados da Federação de Aplicações** para a equipa de suporte do IMPL de navegação da [EAB](mailto:EABTechSupport@eab.com). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-eab-navigate-impl-test-user"></a>Criar o utilizador de teste EAB Navigate IMPL

Nesta secção, cria-se um utilizador chamado B.Simon no EAB Navigate IMPL. Trabalhe com a equipa de [suporte eAB Navigate IMPL](mailto:EABTechSupport@eab.com) para adicionar os utilizadores na plataforma EAB Navigate IMPL. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

## <a name="test-sso"></a>Testar SSO

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clicar no azulejo EAB Navigate IMPL no Painel de Acesso, deve ser automaticamente inscrito no IMPL de navegação EAB para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o EAB Navigate IMPL com a Azure AD](https://aad.portal.azure.com/)
