---
title: 'Tutorial: Azure Ative Directory integração individual (SSO) com BlueJeans para Azure AD [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o BlueJeans para o Azure AD.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfc38f63c5b6361122c236543320b91d22faa70a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "72595039"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bluejeans-for-azure-ad"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com BlueJeans para Azure AD

Neste tutorial, você vai aprender a integrar BlueJeans para Azure AD com Azure Ative Directory (Azure AD). Quando integrar blueJeans para Azure AD com Azure AD, pode:

* Controle em Azure AD que tem acesso a BlueJeans para Azure AD.
* Ative que os seus utilizadores sejam automaticamente inscritos no BlueJeans para a Azure AD com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* BlueJeans para entrada única de AD Azure (SSO) ativada por subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* BlueJeans para Azure AD suporta **SP** iniciado SSO

* BlueJeans para Azure AD suporta fornecimento [ **automatizado** de utilizadores](bluejeans-provisioning-tutorial.md)

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia, pelo que apenas uma instância pode ser configurada num inquilino.

## <a name="adding-bluejeans-for-azure-ad-from-the-gallery"></a>Adicionar BlueJeans para Azure AD da galeria

Para configurar a integração de BlueJeans para Azure AD em Azure AD, você precisa adicionar BlueJeans para Azure AD da galeria para a sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **da galeria,** **digite BlueJeans para Azure AD** na caixa de pesquisa.
1. Selecione **BlueJeans para Azure AD** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-bluejeans-for-azure-ad"></a>Configure e teste Azure AD single sign-on para BlueJeans para Azure AD

Configure e teste Azure AD SSO com BlueJeans para Azure AD utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em BlueJeans para a AD Azure.

Para configurar e testar o Azure AD SSO com BlueJeans para Azure AD, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure blueJeans para Azure AD SSO](#configure-bluejeans-for-azure-ad-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Crie BlueJeans para o utilizador de teste Azure AD](#create-bluejeans-for-azure-ad-test-user)** - para ter uma contrapartida de B.Simon em BlueJeans para Azure AD que esteja ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), no BlueJeans para a página de integração de aplicações **da AD Azure,** encontre a secção **Gerir** e selecione um **único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<companyname>.bluejeans.com`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de Sign-On real. Contacte a Equipa de Apoio ao [Cliente Azure AD para](https://support.bluejeans.com/contact) obter o valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na configuração bluejeans para a secção **Azure AD,** copie os URL(s) apropriados com base na sua exigência.

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

Nesta secção, permitirá que a B.Simon utilize um único sign-on Azure, concedendo acesso ao BlueJeans para a AD Azure.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **BlueJeans para Azure AD**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-bluejeans-for-azure-ad-sso"></a>Configure BlueJeans para Azure AD SSO

1. Numa janela diferente do navegador web, inscreva-se no seu BlueJeans para o site da empresa **Azure AD** como administrador.

2. Vá ao **CONSELHO \> DE ADMINISTRAÇÃO \> SEGURANÇA**.

    ![Administração](./media/bluejeans-tutorial/ic785868.png "Administrador")

3. Na secção **SEGURANÇA,** efetue os seguintes passos:

    ![Sinal único SAML em](./media/bluejeans-tutorial/ic785869.png "Sinal único SAML em")

    a. Selecione **SAML Single Sign On**.

    b. **Selecione Ativar o fornecimento automático**.

4. Siga em frente com os seguintes passos:

    ![Caminho do Certificado](./media/bluejeans-tutorial/ic785870.png "Caminho do Certificado")

    a. Clique em **Escolher Ficheiro**, para carregar o certificado codificado base-64 que descarregou do portal Azure.

    b. Na caixa de texto **login URL,** cola o valor do URL de **Login** que copiou do portal Azure.

    c. Na caixa de texto url de alteração de **password,** colá o valor do URL de **Palavra-passe** de alteração que copiou do portal Azure.

    d. Na caixa de texto **logout URL,** cola o valor do URL de **Logout** que copiou do portal Azure.

5. Siga em frente com os seguintes passos:

    ![Salvar alterações](./media/bluejeans-tutorial/ic785874.png "Salvar alterações")

    a. Na caixa de texto `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`id do **utilizador,** escreva .

    b. Na caixa de texto `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` **e-mail,** escreva .

    c. Clique em **GUARDAR ALTERAÇÕES**.

### <a name="create-bluejeans-for-azure-ad-test-user"></a>Criar BlueJeans para utilizador de teste Azure AD

O objetivo desta secção é criar um utilizador chamado B.Simon em BlueJeans para Azure AD. BlueJeans para Azure AD suporta o fornecimento automático de utilizadores, que está por defeito habilitado. Pode encontrar mais detalhes [aqui](bluejeans-provisioning-tutorial.md) sobre como configurar o fornecimento automático de utilizadores.

**Se precisar de criar manualmente o utilizador, execute os seguintes passos:**

1. Inscreva-se no seu BlueJeans para o site da empresa **Azure AD** como administrador.

2. Vá ao ** \> ADMIN \> GERIR UTILIZADORES ADICIONAR UTILIZADOR**.

    ![Administração](./media/bluejeans-tutorial/ic785877.png "Administrador")

    > [!IMPORTANT]
    > O **separador ADD USER** só está disponível se, no **separador SECUTIRY,** **ativar** o fornecimento automático não for verificado.

3. Na secção **ADD USER,** execute os seguintes passos:

    ![Adicionar Utilizador](./media/bluejeans-tutorial/ic785886.png "Adicionar Utilizador")

    a. Na caixa de texto **First Name,** introduza o primeiro nome do utilizador como **B**.

    b. Na caixa de texto **De Apelido,** introduza o último nome de utilizador como **Simon**.

    c. Em Pick a BlueJeans para caixa de texto **Bluee AD Username,** introduza o nome de utilizador de utilizador como **Brittasimon**

    d. In Criar uma caixa de texto **password,** introduza a sua palavra-passe.

    e. Na caixa de texto **da Empresa,** insira a sua Empresa.

    f. Na caixa de texto **'Endereço de e-mail',** introduza o e-mail do utilizador como `b.simon\@contoso.com`.

    g. In **Create a BlueJeans for Azure AD Meeting I.D.**

    h. Em **Pick a Moderador Passcode** box, introduza a sua senha.

    i. Clique EM **CONTINUAR**.

    ![Adicionar Utilizador](./media/bluejeans-tutorial/ic785887.png "Adicionar Utilizador")

    J. Clique em **ADICIONAR USER**.

> [!NOTE]
> Pode utilizar quaisquer outros BlueJeans para ferramentas de criação de conta de utilizador Azure AD ou APIs fornecidas pela BlueJeans para a Azure AD para fornecer contas de utilizador Azure AD.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar nos BlueJeans para azulejos AD Azure no Painel de Acesso, deve ser automaticamente inscrito nos BlueJeans para Azure AD para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente BlueJeans para Azure AD com Azure AD](https://aad.portal.azure.com/)

