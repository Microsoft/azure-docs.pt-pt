---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com Watch by Colors [ Integração de assinaturas únicas do Azure Ative Diretório) com Watch by Colors [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Watch by Colors.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 586a029c-fb8d-4233-b280-103b9ba7102d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d8fdc8ba2337a3be49a4645e48a45120fb0ccec
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "72026089"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-watch-by-colors"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com Watch by Colors

Neste tutorial, aprenderá a integrar o Watch by Colors com o Azure Ative Directory (Azure AD). Quando integrar o Watch by Colors com o Azure AD, pode:

* Controle em Azure AD que tem acesso ao Relógio por Cores.
* Ative que os seus utilizadores sejam automaticamente inscritos para Ver por Cores com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assista por Colors single sign-on (SSO) ativado por subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Watch by Colors suporta **SP e IDP** iniciadoS SSO

## <a name="adding-watch-by-colors-from-the-gallery"></a>Adicionar Relógio por Cores da galeria

Para configurar a integração do Watch by Colors no Azure AD, é necessário adicionar Watch by Colors da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** escreva **Watch by Colors** na caixa de pesquisa.
1. Selecione **Watch by Colors** do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-watch-by-colors"></a>Configure e teste Azure AD único sign-on para relógio por cores

Configure e teste Azure AD SSO com Watch by Colors utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Watch by Colors.

Para configurar e testar o Azure AD SSO com o Watch by Colors, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure o Relógio por Cores SSO](#configure-watch-by-colors-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Create Watch by Colors test user](#create-watch-by-colors-test-user)** - para ter uma contrapartida de B.Simon no Watch by Colors que está ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Watch by Colors,** encontre a secção **Gerir** e selecione um **único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica saml,** a aplicação está pré-configurada no modo iniciado **idp** e os URLs necessários já estão pré-povoados com o Azure. O utilizador precisa de guardar a configuração clicando no botão **Guardar.**

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL:`https://app.colorscorporation.com/login`

1. No **set single sign-on com** a página SAML, na secção Certificado de **Assinatura SAML,** clique no botão de cópia para copiar o Url de **Metadados da Federação** da Aplicação e guarde-o no seu computador.

    ![O link de descarregamento do Certificado](common/copy-metadataurl.png)

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

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso ao Watch by Colors.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Watch by Colors**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-watch-by-colors-sso"></a>Configure relógio por cores SSO

1. Para automatizar a configuração dentro do Watch by Colors, precisa de instalar a extensão de **'Sign-in' de Aplicações Seguras,** clicando em **instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **configurar Watch by Colors** irá direcioná-lo para a aplicação Watch by Colors. A partir daí, forneça as credenciais de administração para assinar no Watch by Colors. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-5.

    ![Configuração de configuração de configuração](common/setup-sso.png)

3. Se quiser configurar o Watch by Colors manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa Watch by Colors como administrador e execute os seguintes passos:

4. No canto superior direito da página, clique em**Definições** > de **Conta** > de perfil**SSO (Single Sign On)**.

    ![Ver por configuração de Cores](./media/watch-by-colors-tutorial/config01.png)

5. Na página **SSO (Single Sign On),** execute os seguintes passos:

    ![Ver por configuração de Cores](./media/watch-by-colors-tutorial/config02.png)

    a. Toggle **Ativar SAML** **a ON**.

    b. Na caixa de texto **URL,** cola o Url de **Metadados da Federação,** que copiou do portal Azure.

    c. Clique **em Importar**, em seguida, os seguintes campos são automaticamente povoados na página.

    d. Clique em **Guardar**.

### <a name="create-watch-by-colors-test-user"></a>Criar relógio por utilizador de teste de cores

Para permitir que os utilizadores de Anúncios Azure assinem o Watch by Colors, devem ser aprovisionados no Watch by Colors. No Watch by Colors, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no Watch by Colors como administrador de segurança.

1. No canto superior direito da página, clique no **perfil** > **Utilizadores** > **Adicionar utilizador**.

    ![Ver por configuração de Cores](./media/watch-by-colors-tutorial/config03.png)

1. Na página detalhes do **utilizador,** execute os seguintes passos:

    ![Ver por configuração de Cores](./media/watch-by-colors-tutorial/config04.png)

    a. Na caixa de texto **First Name,** introduza o primeiro nome do utilizador como **B**.

    b. Na caixa de texto **De Apelido,** introduza o último nome de utilizador como **Simon**.

    c. Na caixa de texto **por e-mail,** introduza o e-mail do utilizador como `B.Simon@contoso.com`.

    d. Na caixa de texto **Password,** introduza a palavra-passe.

    e. Selecione **Permissões** de Conta de acordo com a sua organização.

    f. Clique em **Guardar**.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Watch by Colors no Painel de Acesso, deve ser automaticamente inscrito no Relógio por Cores para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente relógio por cores com anúncio azure](https://aad.portal.azure.com/)

