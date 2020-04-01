---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com enviado [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Envoy.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 71f7afcc-1033-4098-9b7e-4f9f2b26f734
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28f3fca731c9ceb28f66ecd1c178e5c025f80ede
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "70163535"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-envoy"></a>Tutorial: Azure Ative Directory integração individual (SSO) com enviado

Neste tutorial, você aprenderá a integrar o Enviado com o Azure Ative Directory (Azure AD). Quando integrar o enviado com a AD Azure, pode:

* Controlo em Azure AD que tem acesso ao enviado.
* Permita que os seus utilizadores sejam automaticamente inscritos no Enviado com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* O enviado de um único sinal (SSO) permitiu a subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Enviado apoia **SP** iniciado SSO

* Enviado suporta o provisionamento de utilizadores **justo no tempo**

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia, pelo que apenas uma instância pode ser configurada num inquilino.

## <a name="adding-envoy-from-the-gallery"></a>Adicionando enviado da galeria

Para configurar a integração do enviado em Azure AD, você precisa adicionar enviado da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **da galeria,** digite **enviado** na caixa de pesquisa.
1. Selecione **o enviado** do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-envoy"></a>Configure e teste Azure AD único sign-on para enviado

Configure e teste Azure AD SSO com enviado usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Enviado.

Para configurar e testar azure AD SSO com enviado, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure o Enviado SSO](#configure-envoy-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Crie um utilizador](#create-envoy-test-user)** de teste enviado - para ter uma contraparte de B.Simon em enviado que esteja ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações do **Enviado,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://app.envoy.com/a/saml/auth/<company-ID-from-Envoy>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de Sign-On real. Contacte a equipa de apoio ao Cliente do [Enviado](https://envoy.com/contact/) para obter o valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Na secção Certificado de **Assinatura SAML,** clique no botão **Editar** para abrir o diálogo do Certificado de **Assinatura SAML.**

    ![Editar certificado de assinatura SAML](common/edit-certificate.png)

1. Na secção certificado de **assinatura SAML,** copie o **Valor da Impressão Digital** e guarde-o no seu computador.

    ![Copiar valor de impressão digital](common/copy-thumbprint.png)

1. Na secção **De Configuração** do Enviado, copie os URL(s) adequados com base no seu requisito.

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

Nesta secção, permitirá que B.Simon use o único sign-on azure, concedendo acesso ao Enviado.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Enviado**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-envoy-sso"></a>Configure enviado SSO

1. Para automatizar a configuração dentro do Enviado, precisa de instalar a extensão de **navegador Secure-in das Minhas Aplicações** clicando em **instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique no Enviado de **Configuração** irá direcioná-lo para a aplicação Do enviado. A partir daí, forneça as credenciais de administração para assinar com o Enviado. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-7.

    ![Configuração de configuração de configuração](common/setup-sso.png)

3. Se quiser configurar o Enviado manualmente, abra uma nova janela do navegador web e inscreva-se no site da sua empresa de enviados como administrador e execute os seguintes passos:

4. Na barra de ferramentas na parte superior, clique em **Definições**.

    ![Envoy](./media/envoy-tutorial/ic776782.png "Envoy")

5. Clique em **Empresa**.

    ![Empresa](./media/envoy-tutorial/ic776783.png "Empresa")

6. Clique em **SAML**.

    ![SAML](./media/envoy-tutorial/ic776784.png "SAML")

7. Na secção de configuração de **autenticação SAML,** execute os seguintes passos:

    ![Autenticação SAML](./media/envoy-tutorial/ic776785.png "Autenticação SAML")
    
    >[!NOTE]
    >O valor para o ID de localização hq é gerado automaticamente pela aplicação.
    
    a. Na caixa de texto **impressões digitais,** cola o valor de **impressão digital** do certificado, que copiou do portal Azure.
    
    b. Colar valor URL de **Login,** que copiou forma o portal Azure para a caixa de texto **URL HTTP SAML** FORNECEDOR DE IDENTIDADE.
    
    c. Clique em **Guardar alterações**.

### <a name="create-envoy-test-user"></a>Criar o utilizador de teste do Enviado

Nesta secção, uma utilizadora chamada Britta Simon é criada em Enviado. O enviado suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Enviado, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo do Enviado no Painel de Acesso, deve ser automaticamente inscrito no Enviado para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Tente enviado com AD Azure](https://aad.portal.azure.com/)

