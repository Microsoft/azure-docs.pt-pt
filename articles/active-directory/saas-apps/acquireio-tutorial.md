---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com AcquireIO Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o AcquireIO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.openlocfilehash: 5fe070bc1abe0592b3082c597c1812781335448a
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673193"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-acquireio"></a>Tutorial: Azure Ative Directy integração única (SSO) com AcquireIO

Neste tutorial, você vai aprender a integrar a AcquireIO com o Azure Ative Directory (Azure AD). Quando integra a AcquireIO com a AD Azure, pode:

* Controlo em Azure AD que tem acesso à AcquireIO.
* Permitir que os seus utilizadores sejam automaticamente inscritos na AcquireIO com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição ativada por AcquireIO (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* AcquireIO suporta **IDP** iniciado SSO

## <a name="adding-acquireio-from-the-gallery"></a>Adicionar AcquireIO da galeria

Para configurar a integração da AcquireIO no AD Azure, é necessário adicionar a AcquireIO da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **escreva AcquireIO** na caixa de pesquisa.
1. Selecione **AcquireIO** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-acquireio"></a>Configurar e testar Azure AD único sign-on para AcquireIO

Configure e teste Azure AD SSO com AcquireIO usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na AcquireIO.

Para configurar e testar a Azure AD SSO com a AcquireIO, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure AcquireIO SSO](#configure-acquireio-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create AcquireIO test user](#create-acquireio-test-user)** - para ter uma contraparte de B.Simon in AcquireIO que está ligada à representação AD AZure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração da aplicação **AcquireIO,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:  `https://app.acquire.io/ad/<acquire_account_uid>`

    > [!NOTE]
    > O valor não é real. Receberá o URL de resposta real que é explicado mais tarde na secção **Configure AcquireIO** do tutorial. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar AcquireIO,** copie os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à AcquireIO.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **AcquireIO**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-acquireio-sso"></a>Configurar AdquirirIO SSO

1. Para automatizar a configuração dentro do AcquireIO, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

1. Depois de adicionar a extensão ao navegador, clique em **Configurar AcquireIO,** que o direciona para a aplicação AcquireIO. A partir daí, forneça as credenciais de administração para iniciar sinstição na AcquireIO. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-6.

    ![Configuração de configuração](common/setup-sso.png)

1. Se pretender configurar o AcquireIO manualmente, numa janela diferente do navegador web, inscreva-se na AcquireIO como Administrador.

1. Do lado esquerdo do menu, clique na **App Store.**

    ![Screenshot que destaca a App Store.](./media/acquireio-tutorial/config01.png)

1. Desloque-se até ao **Ative Directory** e clique em **Instalar**.

    ![Screenshot que realça a secção Ative Directory e o botão Instalar.](./media/acquireio-tutorial/config02.png)

1. No pop-up ative directory, execute os seguintes passos:

    ![Screnshot que mostra o ecrã do Ative Directory.](./media/acquireio-tutorial/config03.png)

    a. Clique em **Copiar** para copiar o URL de resposta para o seu exemplo e cole-o na caixa de texto **URL de resposta** na secção de **configuração SAML básica** no portal Azure.

    b. Na caixa de texto **url de login,** cole o valor do URL de **login,** que copiou do portal Azure.

    c. Abra o certificado codificado Base64 no Bloco de Notas, copie o seu conteúdo e cole-o na caixa de texto **do Certificado X.509.**

    d. Clique **em Ligar Agora**.

### <a name="create-acquireio-test-user"></a>Criar utilizador de teste AcquireIO

Para permitir que os utilizadores da Azure AD inscrevam-se na AcquireIO, devem ser a provisionados na AcquireIO. Na AcquireIO, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Numa janela diferente do navegador web, inscreva-se na AcquireIO como Administrador.

1. A partir do lado esquerdo do menu, clique em **Perfis** e navegue para **Adicionar Perfil**.

    ![Screenshot que destaca perfis no menu no lado esquerdo do ecrã, bem como a opção Adicionar Perfil.](./media/acquireio-tutorial/config04.png)

1. No pop-up do **cliente Add,** execute os seguintes passos:

    ![Configuração AcquireIO](./media/acquireio-tutorial/config05.png)

    a. Na caixa de texto **Name,** insira o nome do utilizador como **B.simon**.

    b. Na caixa de texto **por e-mail,** insira o e-mail do utilizador como **B.simon@contoso.com** .

    c. Clique **em Submeter.**

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo AcquireIO no Painel de Acesso, deverá ser automaticamente inscrito no AcquireIO para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente a AcquireIO com a Azure AD](https://aad.portal.azure.com/)