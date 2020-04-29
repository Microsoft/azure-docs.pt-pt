---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com a Nuclino [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Nuclino.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 74bbab82-5581-4dcf-8806-78f77c746968
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a75869c257e6b875a00036218b05db5521e8d0b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "72532965"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nuclino"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com a Nuclino

Neste tutorial, você vai aprender a integrar Nuclino com o Azure Ative Directory (Azure AD). Quando integrar o Nuclino com a Azure AD, pode:

* Controlo em Azure AD que tem acesso a Nuclino.
* Permita que os seus utilizadores sejam automaticamente inscritos na Nuclino com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Nuclino single sign-on (SSO) enabled subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Nuclino apoia **SP e IDP** iniciado SSO
* Nuclino suporta o fornecimento de utilizadores **justo no tempo**

## <a name="adding-nuclino-from-the-gallery"></a>Adicionando Nuclino da galeria

Para configurar a integração do Nuclino em Azure AD, você precisa adicionar Nuclino da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **da galeria,** digite **Nuclino** na caixa de pesquisa.
1. Selecione **Nuclino** no painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-nuclino"></a>Configure e teste Azure AD único sign-on para Nuclino

Configure e teste Azure AD SSO com Nuclino utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Nuclino.

Para configurar e testar o Azure AD SSO com o Nuclino, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure o Nuclino SSO](#configure-nuclino-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Crie um utilizador de teste Nuclino](#create-nuclino-test-user)** - para ter uma contrapartida de B.Simon em Nuclino que esteja ligada à representação da AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **nuclino,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** caso deseje configurar a aplicação no modo iniciado do **IDP,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://api.nuclino.com/api/sso/<UNIQUE-ID>/metadata`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://api.nuclino.com/api/sso/<UNIQUE-ID>/acs`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de Identificação e Resposta real da secção **autenticação,** o que é explicado mais tarde neste tutorial.

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://app.nuclino.com/<UNIQUE-ID>/login`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação, resposta real e URL de sinalização. Contacte a equipa de suporte ao [Cliente nuclino](mailto:contact@nuclino.com) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

6. A aplicação Nuclino espera que as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos saml. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/edit-attribute.png)

7. Além de acima, a aplicação Nuclino espera que poucos atributos sejam retransmitidos na resposta SAML que são mostradas abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Nome |  Atributo fonte|
    | ---------------| --------- |
    | first_name | user.givenname |
    | last_name | utilizador.sobrenome |

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção **Configurar nuclino,** copie os URL(s) adequados com base na sua exigência.

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

Nesta secção, permitirá que b.Simon use o único sign-on Azure, concedendo acesso ao Nuclino.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Nuclino**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-nuclino-sso"></a>Configure Nuclino SSO

1. Para automatizar a configuração dentro do Nuclino, é necessário instalar a extensão de **navegador Secure-in das Minhas Aplicações** clicando em **instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Configurar Nuclino** irá direcioná-lo para a aplicação Nuclino. A partir daí, forneça as credenciais de administração para assinar em Nuclino. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-7.

    ![Configuração de configuração de configuração](common/setup-sso.png)

3. Se pretender configurar a Nuclino manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa Nuclino como administrador e execute os seguintes passos:

4. Clique no **ICON**.

    ![Configuração Nuclino](./media/nuclino-tutorial/configure1.png)

5. Clique no **Azure AD SSO** e selecione **as definições de equipa** a partir do dropdown.

    ![Configuração Nuclino](./media/nuclino-tutorial/configure2.png)

6. **Selecione Autenticação** a partir do painel de navegação esquerdo.

    ![Configuração Nuclino](./media/nuclino-tutorial/configure3.png)

7. Na secção **autenticação,** execute os seguintes passos:

    ![Configuração Nuclino](./media/nuclino-tutorial/configure4.png)

    a. Selecione **um único sign-on baseado em SAML (SSO)**.

    b. Copiar **o URL ACS (Tem de copiar e colar isto ao seu fornecedor SSO)** e colar na caixa de texto URL de **resposta** da secção **de configuração Básica SAML** no portal Azure.

    c. Copiar **Id da entidade (Você precisa copiar e colar isto ao seu fornecedor SSO)** valor e colar na caixa de texto **identificador** da secção **de configuração Básica SAML** no portal Azure.

    d. Na caixa de texto **SSO URL,** colá o valor URL de **Login** que copiou do portal Azure.

    e. Na caixa de texto **Id da Entidade,** colá o valor do **Identificador AD Azure** que copiou do portal Azure.

    f. Abra o ficheiro Certificado descarregado **(Base64)** no Bloco de Notas. Copie o conteúdo da mesmas na sua pasta e, em seguida, cole-o na caixa de texto do **certificado público.**

    g. Clique em **GUARDAR ALTERAÇÕES**.

### <a name="create-nuclino-test-user"></a>Criar o utilizador de teste Nuclino

Nesta secção, um utilizador chamado B.Simon é criado em Nuclino. A Nuclino suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir em Nuclino, um novo é criado após a autenticação.

> [!Note]
> Se precisar de criar um utilizador manualmente, contacte a equipa de [suporte da Nuclino](mailto:contact@nuclino.com).

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Nuclino no Painel de Acesso, deve ser automaticamente inscrito no Nuclino para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente O Nuclino com Azure AD](https://aad.portal.azure.com/)

