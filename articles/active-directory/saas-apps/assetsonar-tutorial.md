---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com A AssetSonar [ Integração de Diretório Ativo) com a AssetSonar [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o AssetSonar.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7cf45a1b-30ce-420d-97c7-6bc36db07b1f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7b2f10a7d784fa7e089484afd5558c4c1db2275
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74896697"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-assetsonar"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com A AssetSonar

Neste tutorial, você vai aprender a integrar o AssetSonar com o Azure Ative Directory (Azure AD). Quando integrar o AssetSonar com a Azure AD, pode:

* Controlo em Azure AD que tem acesso a AssetSonar.
* Ative que os seus utilizadores sejam automaticamente inscritos no AssetSonar com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A subscrição ativada por um único sinal (SSO) do AssetSonar.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* AssetSonar suporta **SP** iniciado SSO
* AssetSonar suporta o fornecimento de utilizadores **justo no tempo**

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia, pelo que apenas uma instância pode ser configurada num inquilino.

## <a name="adding-assetsonar-from-the-gallery"></a>Adicionando AssetSonar da galeria

Para configurar a integração do AssetSonar em Azure AD, precisa de adicionar AssetSonar da galeria à sua lista de aplicações geridas do SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, **digite AssetSonar** na caixa de pesquisa.
1. Selecione **AssetSonar** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-assetsonar"></a>Configure e teste Azure AD único sign-on para AssetSonar

Configure e teste Azure AD SSO com AssetSonar utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em AssetSonar.

Para configurar e testar o Azure AD SSO com o AssetSonar, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure o AssetSonar SSO](#configure-assetsonar-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * Criar o utilizador de **[teste AssetSonar](#create-assetsonar-test-user)** - para ter uma contrapartida de B.Simon em AssetSonar que esteja ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **AssetSonar,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<SUBDOMAIN>.assetsonar.com/users/sign_in`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de Sign-On real. Contacte a equipa de suporte ao [Cliente AssetSonar](mailto:support@assetsonar.com) para obter o valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. A aplicação AssetSonar espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação AssetSonar espera que poucos atributos sejam retransmitidos na resposta SAML que são mostradas abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Nome | Atributo fonte|
    | ------------| --------- |
    | first_name | user.givenname |
    | last_name | utilizador.sobrenome |

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção **Configurar O ActivoSonar,** copie os URL(s) adequados com base no seu requisito.

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

Nesta secção, permitirá que a B.Simon utilize um único sign-on azure, concedendo acesso ao AssetSonar.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **AssetSonar**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-assetsonar-sso"></a>Configure AssetSonar SSO

Para configurar um único sinal no lado do **AssetSonar,** é necessário enviar o Certificado descarregado **(Base64)** e URLs copiados apropriados do portal Azure para a equipa de [suporte AssetSonar](mailto:support@assetsonar.com). Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

### <a name="create-assetsonar-test-user"></a>Criar o utilizador de teste AssetSonar

Nesta secção, um utilizador chamado B.Simon é criado em AssetSonar. O AssetSonar suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no AssetSonar, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo AssetSonar no Painel de Acesso, deverá ser automaticamente inscrito no AssetSonar para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente AssetSonar com Azure AD](https://aad.portal.azure.com/)