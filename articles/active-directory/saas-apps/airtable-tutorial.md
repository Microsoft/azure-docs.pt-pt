---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com airtable  Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Airtable.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7dbf0efa-747c-4d44-b6c3-3438ab38abb6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 12/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 883dd7caabba48b42aaeaeddbe854ddf42973cb4
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2020
ms.locfileid: "79136573"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-airtable"></a>Tutorial: Azure Ative Directory integração individual (SSO) com airtable

Neste tutorial, você vai aprender a integrar a Airtable com o Azure Ative Directory (Azure AD). Quando integrar a Airtable com a Azure AD, pode:

* Controlo em Azure AD que tem acesso à Airtable.
* Ative que os seus utilizadores sejam automaticamente inscritos na Airtable com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A subscrição ativada por um único sinal (SSO) da Airtable.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Airtable suporta **SP e IDP** iniciadoS SSO
* Airtable suporta o fornecimento de utilizadores **justo no tempo**
* Assim que configurar o Airtable, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia, pelo que apenas uma instância pode ser configurada num inquilino.

## <a name="adding-airtable-from-the-gallery"></a>Adicionando airtable da galeria

Para configurar a integração da Airtable em Azure AD, você precisa adicionar Airtable da galeria à sua lista de aplicações saaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **Airtable** na caixa de pesquisa.
1. Selecione **Airtable** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-airtable"></a>Configure e teste Azure AD único sign-on para Airtable

Configure e teste Azure AD SSO com Airtable utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Airtable.

Para configurar e testar o Azure AD SSO com a Airtable, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure o Airtable SSO](#configure-airtable-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Create Airtable test user](#create-airtable-test-user)** - para ter uma contrapartida de B.Simon na Airtable que esteja ligada à representação azure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configure Azure AD SSO

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Airtable,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set-up single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** o utilizador não tem de realizar qualquer passo, uma vez que a aplicação já está pré-integrada com o Azure.

    ![Airtable Domain e URLs informações únicas de inscrição](common/preintegrated.png)

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL: `https://airtable.com/sso/login`

1. Clique em **Guardar**.

1. A aplicação Airtable espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação Airtable espera que poucos atributos sejam retransmitidos na resposta SAML, que são mostradas abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Nome | Atributo fonte|
    | ------------ | --------- |
    | urn:oid:2.5.4.4 | utilizador.sobrenome |
    | urn:oid:2.5.4.42 | user.givenname |

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

1. Na secção Configurar a **Tabela Aérea,** copie os URL(s) adequados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **de nome do utilizador,** introduza o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso à Airtable.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Airtable**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-airtable-sso"></a>Configure Airtable SSO

Siga as instruções dadas no [link](https://support.airtable.com/hc/articles/360037475234) para configurar um único sinal no lado **Airtable.**

### <a name="create-airtable-test-user"></a>Criar utilizador de teste Airtable

Nesta secção, um utilizador chamado B.Simon é criado na Airtable. A Airtable suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Airtable, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clicar no azulejo Airtable no Painel de Acesso, deve ser automaticamente inscrito na Tabela Aérea para a qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](/azure/active-directory/manage-apps/what-is-single-sign-on)

- [O que é o acesso condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
- [Experimente a Airtable com a AD Azure](https://aad.portal.azure.com/)