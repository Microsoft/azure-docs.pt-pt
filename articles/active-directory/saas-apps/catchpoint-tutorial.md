---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com Catchpoint  Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Catchpoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ab3eead7-8eb2-4c12-bb3a-0e46ec899d37
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c56d34a331821ffbc3d0d4f2cf5e9b033f4011ff
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "78968502"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-catchpoint"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com catchpoint

Neste tutorial, você vai aprender a integrar Catchpoint com o Azure Ative Directory (Azure AD). Quando integrar o Catchpoint com o Azure AD, pode:

* Controlo em Azure AD que tem acesso a Catchpoint.
* Ative que os seus utilizadores sejam automaticamente inscritos no Catchpoint com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A subscrição ativada por um único sinal de catchpoint (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Catchpoint suporta **SP e IDP** iniciadoS SSO
* Catchpoint suporta o fornecimento de utilizadores **justo no tempo**
* Assim que configurar o Catchpoint, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

## <a name="adding-catchpoint-from-the-gallery"></a>Adicionando Catchpoint da galeria

Para configurar a integração do Catchpoint em Azure AD, precisa de adicionar o Catchpoint da galeria à sua lista de aplicações saaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **Catchpoint** na caixa de pesquisa.
1. Selecione **Catchpoint** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-catchpoint"></a>Configure e teste Azure AD único sign-on para Catchpoint

Configure e teste Azure AD SSO com Catchpoint utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Catchpoint.

Para configurar e testar o Azure AD SSO com o Catchpoint, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure catchpoint SSO](#configure-catchpoint-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Create Catchpoint test user](#create-catchpoint-test-user)** - para ter uma contraparte de B.Simon em Catchpoint que esteja ligada à representação azure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configure Azure AD SSO

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **catchpoint,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. Na **configuração de um único sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Se pretender configurar a aplicação no modo iniciado do **IDP,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **identificador,** digite o URL: `https://portal.catchpoint.com/SAML2`

    b. Na caixa de texto **URL resposta,** digite o URL: `https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva o URL: `https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`

1. A aplicação Catchpoint espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação Catchpoint espera que poucos atributos sejam retransmitidos na resposta SAML que são mostradas abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Nome | Atributo fonte|
    | ------------ | --------- |
    | espaço de nomes | user.assignedrole |

    > [!NOTE]
    > a reivindicação do espaço de nome tem de ser mapeada com o nome da conta. Este nome de conta deve ser configurado como as funções em Azure AD que serão retransmitidas na resposta SAML. Por favor, consulte este [artigo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) para aprender a configurar os papéis

1. Na **configuração de um único sessão com** a página SAML, na secção Certificado de **Assinatura SAML,** encontre **certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

1. Na secção **'Ponto de captura' configurar,** copie os URL(s) adequados com base no seu requisito.

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

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso ao Catchpoint.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Catchpoint**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-catchpoint-sso"></a>Configure Catchpoint SSO

1. Numa janela diferente do navegador web, inscreva-se na aplicação Catchpoint como administrador.

1. Clique no ícone **Definições** e selecione **SSO Identity Provider**.

    ![Configuração do ponto de captura](./media/catchpoint-tutorial/configuration1.png)

1. Na página **Single Sign On,** execute os seguintes passos:

    ![Configuração do ponto de captura](./media/catchpoint-tutorial/configuration2.png)

    1. Na caixa de texto **Namespace,** introduza um valor de espaço de nome válido.

    1. Na caixa de texto **emitentes** de fornecedor de identidade, introduza o valor do **Identificador AD Azure,** que copiou do portal Azure.

    1. Na caixa de texto **Single Sign On Url,** introduza o valor URL de **Login,** que copiou do portal Azure.

    1. Abra o ficheiro Certificado descarregado **(Base64)** no Bloco de Notas, copie o conteúdo do ficheiro de certificado e cole-o na caixa de texto **do Certificado.**

    1. Também pode carregar os **Metadados da Federação XML** clicando na opção **Enviar Metadados.**

    1. Clique em **Guardar**.

### <a name="create-catchpoint-test-user"></a>Criar o utilizador de teste catchpoint

Nesta secção, um utilizador chamado Britta Simon é criado em Catchpoint. O Catchpoint suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Catchpoint, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clicar no azulejo Catchpoint no Painel de Acesso, deve ser automaticamente inscrito no Ponto de Captura para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

> [!NOTE]
> Quando estiver a iniciar sessão na aplicação Catchpoint através da página de login, depois de fornecer credenciais de ponto de **captura,** introduza o valor **de espaço de nome** válido na caixa de texto **credenciais da empresa (SSO)** e clique **em Login**.

![Configuração do ponto de captura](./media/catchpoint-tutorial/loginimage.png)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [O que é o acesso condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Catchpoint com a AD Azure](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)