---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Fluxx Labs [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Fluxx Labs.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d8fac770-bb57-4e1f-b50b-9ffeae239d07
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 05/21/2020
ms.author: jeedes
ms.openlocfilehash: 198e93fe03c78313dfd64da9b58f2aa9098343f2
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83799280"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fluxx-labs"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com fluxx Labs

Neste tutorial, você vai aprender a integrar fluxx Labs com O Diretório Ativo Azure (Azure AD). Quando integrar o Fluxx Labs com o Azure AD, pode:

* Controlo em Azure AD que tem acesso a Fluxx Labs.
* Permita que os seus utilizadores sejam automaticamente inscritos nos Laboratórios Fluxx com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura ativada por um único sinal (SSO) do Fluxx Labs (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Fluxx Labs suporta **IDP** iniciado SSO
* Assim que configurar o Fluxx Labs, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

## <a name="adding-fluxx-labs-from-the-gallery"></a>Adicionar Fluxx Labs da galeria

Para configurar a integração dos Fluxx Labs em Azure AD, você precisa adicionar Fluxx Labs da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **de galeria,** digite **Fluxx Labs** na caixa de pesquisa.
1. Selecione **Fluxx Labs** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-fluxx-labs"></a>Configure e teste Azure AD único sign-on para Fluxx Labs

Configure e teste Azure AD SSO com Fluxx Labs utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado nos Laboratórios Fluxx.

Para configurar e testar o Azure AD SSO com o Fluxx Labs, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure Fluxx Labs SSO](#configure-fluxx-labs-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Crie o utilizador de teste Fluxx Labs](#create-fluxx-labs-test-user)** - para ter uma contrapartida de B.Simon em Fluxx Labs que está ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **fluxx Labs,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na configuração de um único sign-on com a página **SAML,** execute os seguintes passos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:

    | Ambiente | Padrão URL|
    |-------------|------------|
    | Produção | `https://<subdomain>.fluxx.io` |
    | Pré-produção | `https://<subdomain>.preprod.fluxxlabs.com`|

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:

    | Ambiente | Padrão URL|
    |-------------|------------|
    | Produção | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | Pré-produção | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e resposta real. Contacte a equipa de suporte ao cliente da [Fluxx Labs](https://fluxx.zendesk.com) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção **set Up Fluxx Labs,** copie os URL(s) adequados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de **nome do Utilizador,** introduza o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, você permitirá que B.Simon use o único sign-on Azure, concedendo acesso aos Laboratórios Fluxx.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Fluxx Labs**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-fluxx-labs-sso"></a>Configure Fluxx Labs SSO

1. Numa janela diferente do navegador web, inscreva-se no site da empresa Fluxx Labs como administrador.

2. Selecione **Administrador** abaixo da secção **Definições.**

    ![Configuração de Laboratórios Fluxx](./media/fluxxlabs-tutorial/config1.png)

3. No Painel de Administração, selecione integrações de **plug-ins**  >  **Integrations** e, em seguida, selecione **SAML SSO-(Deficiente)**

    ![Configuração de Laboratórios Fluxx](./media/fluxxlabs-tutorial/config2.png)

4. Na secção de atributos, execute os seguintes passos:

    ![Configuração de Laboratórios Fluxx](./media/fluxxlabs-tutorial/config3.png)

    a. Selecione a caixa de verificação **SAML SSO.**

    b. Na caixa de texto **Rota de Pedido,** tipo **/auth/saml**.

    c. Na caixa de texto **Callback Path,** tipo **/auth/saml/callback**.

    d. Na caixa de texto **"Single Sign-On URL",** introduza o valor URL de **resposta,** que inseriu no portal Azure.

    e. Na caixa de texto **SP Entity ID,** introduza o valor **identificador,** que introduziu no portal Azure.

    f. Na caixa de texto URL target do fornecedor de **identidade SSO** Target, colá o valor URL de **Login,** que copiou do portal Azure.

    g. Abra o seu certificado codificado base-64 no bloco de notas, copie o conteúdo do mesmo na sua área de receita e, em seguida, cole-o na caixa de texto do Certificado de Fornecedor de **Identidade.**

    h. Em **Nome identificador Formato** de texto, introduza o valor `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress` .

    i. Clique em **Guardar**.

    > [!NOTE]
    > Uma vez guardado o conteúdo, o campo aparecerá em branco para a segurança, mas o valor foi guardado na configuração.

### <a name="create-fluxx-labs-test-user"></a>Criar o utilizador de teste fluxx Labs

Para permitir que os utilizadores de Anúncios Azure inscrevam-se nos Laboratórios Fluxx, devem ser aprovisionados nos Laboratórios Fluxx. No caso da Fluxx Labs, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no site da empresa Fluxx Labs como administrador.

2. Clique no **ícone**abaixo apresentado .

    ![Configuração de Laboratórios Fluxx](./media/fluxxlabs-tutorial/config6.png)

3. No painel de instrumentos, clique no ícone abaixo exibido para abrir o cartão **New PEOPLE.**

    ![Configuração de Laboratórios Fluxx](./media/fluxxlabs-tutorial/config4.png)

4. Na secção **NEW PEOPLE,** execute os seguintes passos:

    ![Configuração de Laboratórios Fluxx](./media/fluxxlabs-tutorial/config5.png)

    a. Os Laboratórios Fluxx usam o e-mail como o identificador único para logins SSO. Povoe o campo **SSO UID** com o endereço de e-mail do utilizador, que corresponda ao endereço de e-mail, que estão a usar como login com SSO.

    b. Clique em **Guardar**.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Fluxx Labs no Painel de Acesso, deve ser automaticamente inscrito nos Laboratórios Fluxx para os quais configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente os Laboratórios Fluxx com AD Azure](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger o Fluxx Labs com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)