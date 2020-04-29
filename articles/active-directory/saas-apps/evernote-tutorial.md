---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com o Evernote [ Diretivo Ativo) integração com o Evernote [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Evernote.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55216e9eb4bd5cda2635564480751459f05e8309
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "71121632"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-evernote"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com o Evernote

Neste tutorial, você vai aprender a integrar o Evernote com o Azure Ative Directory (Azure AD). Quando integrar o Evernote com a Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Evernote.
* Permita que os seus utilizadores sejam automaticamente inscritos no Evernote com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A subscrição ativada por um único sinal (SSO) da Evernote.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Evernote apoia **SP e IDP** iniciado SSO

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia, pelo que apenas uma instância pode ser configurada num inquilino.

## <a name="adding-evernote-from-the-gallery"></a>Adicionando Evernote da galeria

Para configurar a integração do Evernote em Azure AD, precisa de adicionar o Evernote da galeria à sua lista de aplicações geridas do SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **da galeria,** digite **Evernote** na caixa de pesquisa.
1. Selecione **Evernote** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-evernote"></a>Configure e teste Azure AD único sign-on para Evernote

Configure e teste Azure AD SSO com Evernote utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Evernote.

Para configurar e testar o Azure AD SSO com o Evernote, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure o Evernote SSO](#configure-evernote-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Create Evernote test user](#create-evernote-test-user)** - para ter uma contrapartida de B.Simon no Evernote que está ligada à representação da AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Evernote,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** caso deseje configurar a aplicação no modo iniciado do **IDP,** introduza os valores para os seguintes campos:

    Na caixa de texto **identificador,** digite um URL:`https://www.evernote.com/saml2`

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL:`https://www.evernote.com/Login.action`

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

7. Para modificar as opções de **Assinatura,** clique no botão **Editar** para abrir o diálogo do Certificado de **Assinatura SAML.**

    ![image](common/edit-certificate.png) 

    ![image](./media/evernote-tutorial/samlassertion.png)

    a. Selecione a opção de **resposta e afirmação Sign SAML** para **a Opção de Assinatura**.

    b. Clique em **Guardar**

1. Na secção Configurar o **Evernote,** copie os URL(s) adequados com base no seu requisito.

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

Nesta secção, permitirá que b.Simon use o único sign-on Azure, concedendo acesso ao Evernote.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Evernote**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-evernote-sso"></a>Configure Evernote SSO

1. Para automatizar a configuração dentro do Evernote, precisa de instalar a extensão de **navegador Secure-in das Minhas Aplicações** clicando em **instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **configurar o Evernote** irá direcioná-lo para a aplicação Evernote. A partir daí, forneça as credenciais de administração para assinar no Evernote. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-6.

    ![Configuração de configuração de configuração](common/setup-sso.png)

3. Se quiser configurar o Evernote manualmente, abra uma nova janela do navegador web e inscreva-se no site da sua empresa Evernote como administrador e execute os seguintes passos:

4. Ir a **'Consola De Administrador'**

    ![Admin-Consola](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

5. A partir da **'Consola de Administrador',** vá a **'Segurança'** e selecione **'Single Sign-On'**

    ![Definição sso](./media/evernote-tutorial/tutorial_evernote_sso.png)

6. Configure os seguintes valores:

    ![Definição de certificado](./media/evernote-tutorial/tutorial_evernote_certx.png)
    
    a.  **Ativar sso:** SSO está ativado por padrão (Clique **em desativar um único sinal para** remover o requisito SSO)

    b. Colar o valor URL de **Login,** que copiou do portal Azure para a caixa de texto **URL SAML HTTP Request** URL.

    c. Abra o certificado descarregado a partir de Azure AD num bloco de notas e copie o conteúdo incluindo "CERTIFICADO DE INÍCIO" e "CERTIFICADO FINAL" e cole-o na caixa de texto **x.509 do Certificado.** 

    d.Clique **salvar alterações**

### <a name="create-evernote-test-user"></a>Criar o utilizador de teste Evernote

Para permitir que os utilizadores da AD Azure assinem o Evernote, devem ser aprovisionados no Evernote.  
No caso do Evernote, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no site da sua empresa Evernote como administrador.

2. Clique na **'Consola de Administrador'.**

    ![Admin-Consola](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

3. A partir da **'Consola de Administrador',** vá a **'Adicionar utilizadores'.**

    ![Adicionar utilizadores](./media/evernote-tutorial/create_aaduser_0001.png)

4. **Adicione os membros** da equipa na caixa de texto de **e-mail,** escreva o endereço de e-mail da conta do utilizador e clique **em Convidar.**

    ![Adicionar utilizadores](./media/evernote-tutorial/create_aaduser_0002.png)
    
5. Após o convite ser enviado, o titular da conta Azure Ative Diretório receberá um e-mail para aceitar o convite.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Evernote no Painel de Acesso, deverá ser automaticamente inscrito no Evernote para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente evernote com Azure AD](https://aad.portal.azure.com/)

