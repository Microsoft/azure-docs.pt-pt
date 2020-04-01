---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com Drift [ Integração de um único sign-on do Azure Ative Diretório) com drift [ Drift ] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Drift.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 39dcbb95-c192-448c-86a1-cedede1c0972
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0cd749ef66ee62f6d89d949cef7ce800bc46d59a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "72554366"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-drift"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com Drift

Neste tutorial, você vai aprender a integrar drift com Azure Ative Directory (Azure AD). Quando integrar a Drift com a Azure AD, pode:

* Controlo em Azure AD que tem acesso a Drift.
* Permita que os seus utilizadores sejam automaticamente inscritos na Drift com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura ativada por um único sinal de deriva (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Drift suporta **SP e IDP** iniciadoS SSO
* Drift suporta aprovisionamento de utilizadores **justo no tempo**

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia, pelo que apenas uma instância pode ser configurada num inquilino.

## <a name="adding-drift-from-the-gallery"></a>Adicionando deriva da galeria

Para configurar a integração do Drift em Azure AD, você precisa adicionar Drift da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **da galeria,** digite **Drift** na caixa de pesquisa.
1. Selecione **Drift** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-drift"></a>Configure e teste Azure AD único sign-on para Drift

Configure e teste Azure AD SSO com Drift utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Drift.

Para configurar e testar o Azure AD SSO com drift, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure drift SSO](#configure-drift-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Create Drift test user](#create-drift-test-user)** - para ter uma contrapartida de B.Simon em Drift que esteja ligada à representação azure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Drift,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção de  **Configuração Básica saml,** a aplicação está pré-configurada no modo iniciado **idp** e os URLs necessários já estão pré-povoados com o Azure. O utilizador precisa de guardar a configuração clicando no botão **Guardar.** 

    a. Clique em **definir URLs adicionais**.
 
    b. Na caixa de texto **do Estado relé,** escreva um URL:`https://app.drift.com` 

    c. Se desejar configurar a aplicação no modo iniciado **sp,** execute o seguinte passo:

    d. Na caixa de texto **de URL sign-on,** escreva um URL:`https://start.drift.com`

6. A sua aplicação Drift espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos saml. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/edit-attribute.png)

7. Além de acima, a aplicação Drift espera que poucos atributos sejam retransmitidos na resposta SAML que são mostradas abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com o seu requisito. 

    | Nome | Atributo fonte|
    | ---------------| --------------- |    
    | Nome | user.displayname |

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **metadados da Federação XML** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

1. Na secção **'set up Drift',** copie os URL(s) adequados com base no seu requisito.

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

Nesta secção, permitirá que b.Simon use o único sign-on Azure, concedendo acesso ao Drift.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Drift**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-drift-sso"></a>Configure Drift SSO

1. Para automatizar a configuração dentro do Drift, precisa de instalar a extensão de **navegador Secure-in das Minhas Aplicações** clicando em **instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **'Setup Drift'** irá direcioná-lo para a aplicação Drift. A partir daí, forneça as credenciais de administração para assinar em Drift. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-4.

    ![Configuração de configuração de configuração](common/setup-sso.png)

3. Se quiser configurar o Drift manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa Drift como administrador e execute os seguintes passos:

4. Do lado esquerdo da barra de menus, clique em Definições de**definições** > do **ícone** > **Definições de Definições** e execute os seguintes passos:

    ![A ligação de administrador](./media/drift-tutorial/tutorial_drift_admin.png)

    a. Faça upload do **Federation Metadata XML** que descarregou do portal Azure, para a caixa de texto de ficheiros de ficheiros do Fornecedor de **Identidade de Upload.**

    b. Depois de carregar o ficheiro de metadados, os valores restantes são automaticamente povoados na página.

    c. Clique em **ativar saml**.

### <a name="create-drift-test-user"></a>Criar utilizador de teste drift

Nesta secção, um utilizador chamado Britta Simon é criado em Drift. A drift suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Drift, um novo é criado após a autenticação.

>[!Note]
>Se precisar de criar um utilizador manualmente, contacte a equipa de [suporte drift](mailto:integrations@drift.com).

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Drift no Painel de Acesso, deve ser automaticamente inscrito no Drift para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente drift com Azure AD](https://aad.portal.azure.com/)

