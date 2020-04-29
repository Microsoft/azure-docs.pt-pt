---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com Discovery Benefits SSO [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Discovery Benefits SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a788cd07-0eed-4067-b79d-19b840e8836d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64c4a6811ef5d7ed4f29c7dae89561616895a42a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "72266144"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-discovery-benefits-sso"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com Discovery Benefits SSO

Neste tutorial, você vai aprender a integrar Discovery Benefits SSO com Azure Ative Directory (Azure AD). Quando integrar o Discovery Benefits SSO com a Azure AD, pode:

* Controle em Azure AD que tem acesso a Discovery Benefits SSO.
* Ative que os seus utilizadores sejam automaticamente inscritos no Discovery Benefits SSO com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Discovery Benefits SSO single sign-on (SSO) enabled subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Discovery Benefits SSO suporta **IDP** iniciado SSO

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia, pelo que apenas uma instância pode ser configurada num inquilino.

## <a name="adding-discovery-benefits-sso-from-the-gallery"></a>Adicionar Benefícios discovery SSO da galeria

Para configurar a integração do Discovery Benefits SSO em Azure AD, precisa adicionar Discovery Benefits SSO da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **Discovery Benefits SSO** na caixa de pesquisa.
1. Selecione **Discovery Benefits SSO** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-discovery-benefits-sso"></a>Configure e teste Azure AD único sign-on para Discovery Benefits SSO

Configure e teste Azure AD SSO com Discovery Benefits SSO utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Discovery Benefits SSO.

Para configurar e testar o Azure AD SSO com Discovery Benefits SSO, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure os benefícios da descoberta SSO SSO](#configure-discovery-benefits-sso-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Create Discovery Benefits SSO test user](#create-discovery-benefits-sso-test-user)** - para ter uma contrapartida de B.Simon em Discovery Benefits SSO que está ligado à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Discovery Benefits SSO,** encontre a secção **Gerir** e selecione um **único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção de  **Configuração Básica saml,** a aplicação está pré-configurada no modo iniciado **idp** e os URLs necessários já estão pré-povoados com o Azure. O utilizador precisa de guardar a configuração clicando no botão **Guardar.** 

1. A aplicação Discovery Benefits SSO espera que as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos. Clique no ícone **Editar** para abrir o diálogo de Atributos do Utilizador.

    ![image](common/edit-attribute.png)

    a. Clique no ícone **Editar** para abrir o diálogo **unique user identifier (Name ID).**

    ![Descoberta Benefits SSO configuração](./media/discovery-benefits-sso-tutorial/attribute01.png)

    ![Descoberta Benefits SSO configuração](./media/discovery-benefits-sso-tutorial/attribute02.png)

    b. Clique no ícone **Editar** para abrir o diálogo de **transformação Gerir.**

    c. Na caixa de texto **Transformation,** digite o **ToUppercase()** mostrado para essa linha.

    d. Na caixa de texto **parametria 1,** digite o parâmetro como `<Name Identifier value>`.

    e. Clique em **Adicionar**.

    > [!NOTE]
    > O Discovery Benefits SSO requer que um valor fixo de cadeia seja passado no campo **Unique User Identifier (Name ID)** para que esta integração funcione. A AD Azure atualmente não suporta esta funcionalidade para que, como um trabalho ao redor, você pode usar **transformações ToUpper** ou **ToLower** do NameID para definir um valor de cadeia fixo como mostrado acima na imagem.

    f. Povoamos automaticamente as reivindicações adicionais que são necessárias para a configuração SSO (`SSOInstance` e `SSOID`). Use o ícone **Editar** para mapear os valores de acordo com a sua organização.

    ![Descoberta Benefits SSO configuração](./media/discovery-benefits-sso-tutorial/attribute03.png)

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção **Configurar Benefícios Discovery SSO,** copie os URL(s) adequados com base no seu requisito.

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

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso ao Discovery Benefits SSO.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Discovery Benefits SSO**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-discovery-benefits-sso-sso"></a>Configure benefícios de descoberta SSO SSO

Para configurar um único sign-on no lado Discovery **Benefits SSO,** você precisa enviar o Certificado descarregado **(Base64)** e URLs copiados apropriados do portal Azure para [discovery Benefits SSO equipe](mailto:Jsimpson@DiscoveryBenefits.com)de suporte . Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

### <a name="create-discovery-benefits-sso-test-user"></a>Criar discovery benefits SSO test user

Nesta secção, cria-se uma utilizadora chamada Britta Simon no Discovery Benefits SSO. Trabalhe com a equipa de [suporte Discovery Benefits SSO](mailto:Jsimpson@DiscoveryBenefits.com) para adicionar os utilizadores na plataforma Discovery Benefits SSO. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Discovery Benefits SSO no Painel de Acesso, deve ser automaticamente inscrito no Discovery Benefits SSO para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente Discovery Benefits SSO com AD Azure](https://aad.portal.azure.com/)

