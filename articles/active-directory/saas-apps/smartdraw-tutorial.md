---
title: 'Tutorial: Azure Ative Diretório integração de um único sign-on (SSO) com o SmartDraw [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o SmartDraw.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6f8fbbe8-c771-4fa1-9326-5a9dac991ace
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/02/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe08f1523b4b61653d89a9b3472355dd3eeaf69f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75640096"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-smartdraw"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com o SmartDraw

Neste tutorial, aprenderá a integrar o SmartDraw com o Azure Ative Directory (Azure AD). Quando integrar o SmartDraw com o Azure AD, pode:

* Controlo em Azure AD que tem acesso ao SmartDraw.
* Ative que os seus utilizadores sejam automaticamente inscritos no SmartDraw com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A subscrição ativada pelo SmartDraw (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* SmartDraw suporta **SP e IDP** iniciadoS SSO
* SmartDraw suporta o fornecimento de utilizadores **justo no tempo**

## <a name="adding-smartdraw-from-the-gallery"></a>Adicionar SmartDraw da galeria

Para configurar a integração do SmartDraw em Azure AD, precisa de adicionar smartDraw da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **SmartDraw** na caixa de pesquisa.
1. Selecione **SmartDraw** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-smartdraw"></a>Configure e teste Azure AD single sign-on para SmartDraw

Configure e teste Azure AD SSO com SmartDraw utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no SmartDraw.

Para configurar e testar o Azure AD SSO com o SmartDraw, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure smartDraw SSO](#configure-smartdraw-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Create SmartDraw test user](#create-smartdraw-test-user)** - para ter uma contraparte de B.Simon no SmartDraw que está ligada à representação azure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **SmartDraw,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** o utilizador não tem de realizar qualquer passo, uma vez que a aplicação já está pré-integrada com o Azure.

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://cloud.smartdraw.com/sso/saml/login/<domain>`

    > [!NOTE]
    > O valor do URL de inscrição não é real. Atualizará o valor de URL de Sign-on com o URL de sign-on real, o que é explicado mais tarde no tutorial. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Clique em **Guardar**.

1. A aplicação SmartDraw espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação SmartDraw espera que poucos atributos sejam retransmitidos na resposta SAML que são mostradas abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Nome | Atributo fonte|
    | ---------------| --------------- |
    | FirstName | user.givenname |
    | LastName | utilizador.sobrenome |
    | Email | utilizador.mail |
    | Grupos | user.groups |

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **metadados da Federação XML** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

1. Na secção Configurar o **SmartDraw,** copie os URL(s) adequados com base no seu requisito.

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

Nesta secção, permitirá que a B.Simon utilize um único sign-on azure, concedendo acesso ao SmartDraw.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **SmartDraw**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-smartdraw-sso"></a>Configure SmartDraw SSO

1. Para automatizar a configuração dentro do SmartDraw, é necessário instalar a extensão de **'Sign-in' de Aplicações Seguras,** clicando em **instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

1. Depois de adicionar extensão ao navegador, clique em **Configurar o SmartDraw** irá direcioná-lo para a aplicação SmartDraw. A partir daí, forneça as credenciais de administração para assinar no SmartDraw. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-5.

    ![Configuração de configuração de configuração](common/setup-sso.png)

1. Se pretender configurar manualmente o SmartDraw, abra uma nova janela do navegador web e inscreva-se no site da empresa SmartDraw como administrador e execute os seguintes passos:

1. Clique em **Single Sign-On** em Gerir a sua licença SmartDraw.

    ![Configuração SmartDraw](./media/smartdraw-tutorial/configure01.png)

1. Na página de Configuração, execute os seguintes passos:

    ![Configuração SmartDraw](./media/smartdraw-tutorial/configure02.png)

    a. Na caixa de texto **Your Domain (como acme.com),** escreva o seu domínio.

    b. Copie o url de login iniciado pelo **SP será,** por exemplo, e cola-o na caixa de texto url signon na **configuração Básica SAML** no portal Azure.

    c. Nos **Grupos de Segurança para permitir o Acesso SmartDraw,** escreva **todos**.

    d. Na caixa de texto **SAML Emitente Url,** colá-cola o valor do **Identificador AD Azure** que copiou do portal Azure.

    e. No Notepad, abra o ficheiro Metadata XML que descarregou do portal Azure, copie o seu conteúdo e, em seguida, cole-o na caixa **SAML MetaData.**

    f. Clique na **configuração de guardar** 

### <a name="create-smartdraw-test-user"></a>Criar o utilizador de teste SmartDraw

Nesta secção, um utilizador chamado B.Simon é criado no SmartDraw. O SmartDraw suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no SmartDraw, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo SmartDraw no Painel de Acesso, deve ser automaticamente inscrito no SmartDraw para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente smartDraw com Anúncio Azure](https://aad.portal.azure.com/)