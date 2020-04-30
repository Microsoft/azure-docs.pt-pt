---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com KnowledgeOwl [ Integração de assinaturas únicas do Diretório Ativo azure) com a KnowledgeOwl [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o KnowledgeOwl.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ae30996-864d-4872-90bc-f770e1ea159a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc7d481b757a76ba65e0c78a93bde1bc58ace7cc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "72791644"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-knowledgeowl"></a>Tutorial: Azure Ative Directory integração individual (SSO) com KnowledgeOwl

Neste tutorial, você vai aprender a integrar knowledgeOwl com O Diretório Ativo Azure (Azure AD). Quando integrar o KnowledgeOwl com o Azure AD, pode:

* Controlo em Azure AD que tem acesso a KnowledgeOwl.
* Ative que os seus utilizadores sejam automaticamente inscritos no KnowledgeOwl com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura ativada por um único sign-on (SSO) do KnowledgeOwl.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* KnowledgeOwl apoia **SP e IDP** iniciadoS SSO
* KnowledgeOwl suporta o provisionamento de utilizadores **justo no tempo**

## <a name="adding-knowledgeowl-from-the-gallery"></a>Adicionar KnowledgeOwl da galeria

Para configurar a integração do KnowledgeOwl em Azure AD, precisa de adicionar knowledgeOwl da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **KnowledgeOwl** na caixa de pesquisa.
1. Selecione **KnowledgeOwl** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-knowledgeowl"></a>Configure e teste Azure AD único sign-on para KnowledgeOwl

Configure e teste Azure AD SSO com KnowledgeOwl utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no KnowledgeOwl.

Para configurar e testar o Azure AD SSO com o KnowledgeOwl, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure KnowledgeOwl SSO](#configure-knowledgeowl-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Create KnowledgeOwl test user](#create-knowledgeowl-test-user)** - para ter uma contrapartida de B.Simon no KnowledgeOwl que está ligada à representação azure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **KnowledgeOwl,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** caso deseje configurar a aplicação no modo iniciado do **IDP,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:
    
    | | |
    |-|-|
    | `https://app.knowledgeowl.com/sp`|
    | `https://app.knowledgeowl.com/sp/id/<unique ID>`|

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:
    
    | | |
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:
    
    | | |
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|

    > [!NOTE]
    > Estes valores não são reais. Terá de atualizar estes valores a partir de URL de Identificação, Resposta e URL de Entrada real, o que é explicado mais tarde no tutorial.

1. A aplicação KnowledgeOwl espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação KnowledgeOwl espera que poucos atributos sejam retransmitidos na resposta SAML que são mostradas abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Nome | Atributo fonte | Espaço de nomes |
    | ------------ | -------------------- | -----|
    | soóide | utilizador.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **certificado (Cru)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificateraw.png)

1. Na secção **Configurar KnowledgeOwl,** copie os URL(s) adequados com base no seu requisito.

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

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso ao KnowledgeOwl.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **KnowledgeOwl**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-knowledgeowl-sso"></a>Configure KnowledgeOwl SSO

1. Numa janela diferente do navegador web, inscreva-se no site da sua empresa KnowledgeOwl como administrador.

1. Clique em **Definições** e, em seguida, selecione **'Segurança**'.

    ![Configuração KnowledgeOwl](./media/knowledgeowl-tutorial/configure1.png)

1. Percorra a **Integração SAML SSO** e execute os seguintes passos:

    ![Configuração KnowledgeOwl](./media/knowledgeowl-tutorial/configure2.png)

    a. **Selecione ativar saml sso**.

    b. Copie o valor de ID da **Entidade SP** e cole-o no **Identificador (ID** da Entidade) na secção **de Configuração SAML Básica** no portal Azure.

    c. Copie o valor url de **login SP** e cole-o nas caixas de texto **URL de início de sign-on e resposta** na secção de **configuração Básica SAML** no portal Azure.

    d. Na caixa de texto **idid entidade ID,** cola o valor do **Identificador AD Azure,** que copiou do portal Azure.

    e. Na caixa de texto **IDP Login URL,** colá o valor URL de **Login,** que copiou do portal Azure.

    f. Na caixa de texto **IDP Logout URL,** colá o valor URL de **Logout,** que copiou do portal Azure

    g. Faça upload do certificado descarregado do portal Azure clicando no **Certificado IdP upload**.

    h. Clique nos **atributos do Mapa SAML** para mapear atributos e executar os seguintes passos:

    ![Configuração KnowledgeOwl](./media/knowledgeowl-tutorial/configure3.png)

    * Introduza `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ssoid` na caixa de texto **SSO ID**
    * Introduza `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` no **username/email** textbox.
    * Introduza `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` na caixa de texto **First Name.**
    * Introduza `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` na caixa de texto **Do Último Nome.**
    * Clique em **Guardar**

    i. Clique em **Guardar** na parte inferior da página.

    ![Configuração KnowledgeOwl](./media/knowledgeowl-tutorial/configure4.png)

### <a name="create-knowledgeowl-test-user"></a>Criar o utilizador de teste KnowledgeOwl

Nesta secção, um utilizador chamado B.Simon é criado no KnowledgeOwl. O KnowledgeOwl suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no KnowledgeOwl, um novo é criado após a autenticação.

> [!Note]
> Se precisar de criar um utilizador manualmente, contacte a equipa de [suporte KnowledgeOwl](mailto:support@knowledgeowl.com).

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo KnowledgeOwl no Painel de Acesso, deve ser automaticamente inscrito no KnowledgeOwl para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente KnowledgeOwl com Azure AD](https://aad.portal.azure.com/)