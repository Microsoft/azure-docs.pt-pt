---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com spotinst [ Integração de um único sign-on) com spotinst [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Spotinst.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2f6dbd70-c2db-4ae9-99ee-976c3090d214
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/03/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5490ff6c6143dff258d74e013bb9d4c821aab625
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "76263290"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-spotinst"></a>Tutorial: Azure Ative Directory integração individual (SSO) com spotinst

Neste tutorial, você vai aprender a integrar spotinst com O Diretório Ativo Azure (Azure AD). Quando integrar spotinst com AD Azure, pode:

* Controlo em Azure AD que tem acesso a Spotinst.
* Permita que os seus utilizadores sejam automaticamente inscritos no Spotinst com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura de um único sinal (SSO) ativada pelo Spotinst.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Spotinst suporta **SP e IDP** iniciadoS SSO

## <a name="adding-spotinst-from-the-gallery"></a>Adicionando Spotinst da galeria

Para configurar a integração do Spotinst no Azure AD, precisa de adicionar spotinst da galeria à sua lista de aplicações geridas do SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, digite **Spotinst** na caixa de pesquisa.
1. Selecione **Spotinst** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-spotinst"></a>Configure e teste Azure AD único sign-on para Spotinst

Configure e teste Azure AD SSO com Spotinst utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Spotinst.

Para configurar e testar o Azure AD SSO com spotinst, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure spotinst SSO](#configure-spotinst-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Create Spotinst test user](#create-spotinst-test-user)** - para ter uma contrapartida de B.Simon no Spotinst que está ligada à representação da AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Spotinst,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** se pretender configurar a aplicação no modo iniciado **idp,** execute os seguintes passos:

    a. Verifique **Definir URLs adicionais**.

    b. Na caixa de texto **do Estado relé,** escreva um valor:`<ID>`

1. Clique em **Definir URLs adicionais** e execute os seguintes passos se pretender configurar a aplicação no modo iniciado por **SP:**

    Na caixa de texto **de URL sign-on,** escreva o URL:`https://console.spotinst.com/auth/saml`

    > [!NOTE]
    > O valor do Estado relé não é real. Atualizará o valor do Estado de Retransmissão com o valor real do Estado de Retransmissão, o que é explicado mais tarde no tutorial.

1. Clique em **Guardar**.

1. A aplicação Spotinst espera que as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação Spotinst espera que poucos atributos sejam retransmitidos na resposta SAML que são mostradas abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Nome | Atributo fonte|
    | -----| --------------- |
    | Email | utilizador.mail |
    | FirstName | user.givenname |
    | LastName | utilizador.sobrenome |

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **metadados da Federação XML** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

1. Na secção **Configurar spotinst,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, permitirá que b.Simon use o único sign-on Azure, concedendo acesso ao Spotinst.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Spotinst**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-spotinst-sso"></a>Configure Spotinst SSO

1. Numa janela de navegador web diferente, inscreva-se no Spotinst como administrador de segurança.

2. Clique no ícone do **utilizador** no lado superior direito do ecrã e clique em **Definições**.

    ![Definições spotinst](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

3. Clique no separador **SEGURANÇA** na parte superior e, em seguida, selecione **Fornecedores de Identidade** e execute os seguintes passos:

    ![Segurança spotinst](./media/spotinst-tutorial/tutorial_spotinst_security.png)

    a. Copie o valor **do Estado de Retransmissão** para a sua instância e cole-o na caixa de texto **do Estado de Retransmissão** na secção **de configuração SAML Básica** no portal Azure.

    b. Clique em **BROWSE** para carregar o ficheiro xml de metadados que descarregou do portal Azure

    c. Clique em **SAVE**.

### <a name="create-spotinst-test-user"></a>Criar o utilizador de teste Spotinst

O objetivo desta secção é criar uma utilizadora chamada Britta Simon em Spotinst.

1. Se configurar a aplicação no modo iniciado **sp,** execute os seguintes passos:

   a. Numa janela de navegador web diferente, inscreva-se no Spotinst como administrador de segurança.

   b. Clique no ícone do **utilizador** no lado superior direito do ecrã e clique em **Definições**.

    ![Definições spotinst](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

    c. Clique em **Utilizadores** e selecione **ADD USER**.

    ![Definições spotinst](./media/spotinst-tutorial/adduser1.png)

    d. Na secção adicionar utilizador, execute os seguintes passos:

    ![Definições spotinst](./media/spotinst-tutorial/adduser2.png)

    * Na caixa de texto **Nome Completo,** introduza o nome completo de utilizador como **BrittaSimon**.

    * Na caixa de texto **e-mail,** introduza o endereço de e-mail do utilizador como `brittasimon\@contoso.com`.

    * Selecione os detalhes específicos da organização para o Papel da **Organização, Papel de Conta e Contas**.

2. Se configurar a aplicação no modo iniciado do **IDP,** não existe nenhum item de ação para si nesta secção. Spotinst suporta o fornecimento just-in-time, que é por padrão habilitado. Um novo utilizador é criado durante uma tentativa de aceder ao Spotinst se ainda não existir.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Spotinst no Painel de Acesso, deve ser automaticamente inscrito no Spotinst para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente Spotinst com Azure AD](https://aad.portal.azure.com/)

