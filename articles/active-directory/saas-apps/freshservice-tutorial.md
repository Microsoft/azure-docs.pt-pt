---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com Freshservice [ Integração de diretório si) com freshservice [ Integração de diretórios ativos) com freshservice [ Integração de Diretório Ativo) com Freshservice [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Freshservice.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3dd22b1f-445d-45c6-8eda-30207eb9a1a8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e08ef72dca09f873ad1cfcc91e132063b88406b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74227524"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshservice"></a>Tutorial: Azure Ative Directory integração individual (SSO) com freshservice

Neste tutorial, você vai aprender a integrar freshservice com o Azure Ative Directory (Azure AD). Quando integrar o Freshservice com a Azure AD, pode:

* Controlo em Azure AD que tem acesso a Freshservice.
* Permita que os seus utilizadores sejam automaticamente inscritos na Freshservice com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A subscrição ativada por um único sinal de serviço freshservice (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Freshservice suporta **SP** iniciado SSO

## <a name="adding-freshservice-from-the-gallery"></a>Adicionando Freshservice da galeria

Para configurar a integração do Freshservice no Azure AD, precisa de adicionar freshservice da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **Freshservice** na caixa de pesquisa.
1. Selecione **Freshservice** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-freshservice"></a>Configure e teste Azure AD único sign-on para Freshservice

Configure e teste Azure AD SSO com Freshservice utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Freshservice.

Para configurar e testar o Azure AD SSO com freshservice, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure Freshservice SSO](#configure-freshservice-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Create Freshservice test user](#create-freshservice-test-user)** - para ter uma contrapartida de B.Simon em Freshservice que está ligada à representação da AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Freshservice,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<democompany>.freshservice.com`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`https://<democompany>.freshservice.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e identificador. Contacte a equipa de suporte do [Cliente Freshservice](https://support.freshservice.com/) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Na configuração de um único sinal com página **SAML,** na secção certificado de **assinatura SAML,** encontre **certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Freshservice requer impressões digitais SHA-256 para pôr o SSO a funcionar. Para obter a impressão digital SHA-256, execute os seguintes passos:

    ![Impressão de dedo](./media/freshservice-tutorial/ic790821.png "Impressão de dedo")

    1. Abra o [link](https://www.samltool.com/fingerprint.php) em diferentes navegadores web.

    1. Abra o ficheiro de certificado descarregado (Base64) no Bloco de Notas e colhe o conteúdo na caixa de texto **cert X.509.**

    1. Para o Algoritmo, selecione **sha256** a partir da queda.

    1. Clique em **CALCULAR A impressão digital**.

    1. Clique no ícone da cópia para copiar o **FingerPrint** gerado e guardá-lo no seu computador.

1. Na secção **Derespor-se ao Novo Serviço** no **portal Azure,** copie os URL(s) adequados com base na sua exigência.

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

Nesta secção, permitirá que b.Simon use o único sign-on Azure, concedendo acesso ao Freshservice.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Freshservice**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-freshservice-sso"></a>Configure Freshservice SSO

1. Para automatizar a configuração dentro do Freshservice, precisa de instalar a extensão de **navegador Secure-in das Minhas Aplicações** clicando em **instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Configurar Freshservice** irá direcioná-lo para a aplicação Freshservice. A partir daí, forneça as credenciais de administração para assinar no Freshservice. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-6.

    ![Configuração de configuração de configuração](common/setup-sso.png)

3. Se quiser configurar o Freshservice manualmente, abra uma nova janela do navegador web e inscreva-se no site da sua empresa Freshservice como administrador e execute os seguintes passos:

4. No menu em cima, clique **em Admin**.

    ![Administração](./media/freshservice-tutorial/ic790814.png "Administrador")

5. No Portal do **Cliente,** clique em **Segurança.**

    ![Segurança](./media/freshservice-tutorial/ic790815.png "Segurança")

6. Na secção **segurança,** execute os seguintes passos:

    ![Início de Sessão Único](./media/freshservice-tutorial/ic790816.png "Início de Sessão Único")

    a. Ligue **o sinal único ligado**.

    b. Selecione **SAML SSO**.

    c. Na caixa de texto **URL de Login SAML,** colá o valor do URL de **Login,** que copiou do portal Azure.

    d. Na caixa de texto **logout URL,** cola o valor do URL de **Logout,** que copiou do portal Azure.

    e. No Certificado de Segurança, a caixa de texto **Impressões Digitais,** cola o valor **FingerPrint,** que gerou anteriormente.

    f. Clique em **Guardar**

### <a name="create-freshservice-test-user"></a>Criar o utilizador de teste Freshservice

Para permitir que os utilizadores de Anúncios Azure assinem no FreshService, devem ser aprovisionados no FreshService. No caso do FreshService, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no site da sua empresa **FreshService** como administrador.

2. No menu em cima, clique **em Admin**.

    ![Administração](./media/freshservice-tutorial/ic790814.png "Administrador")

3. Na secção **de Gestão** de Utilizadores, clique em **Requesters**.

    ![Requisitadores](./media/freshservice-tutorial/ic790818.png "Requisitadores")

4. Clique em **Novo Requester**.

    ![Novos Requesters](./media/freshservice-tutorial/ic790819.png "Novos Requesters")

5. Na secção **New Requester,** execute os seguintes passos:

    ![Novo Requester](./media/freshservice-tutorial/ic790820.png "Novo Requester")  

    a. Introduza os atributos **de Primeiro Nome** e **E-mail** de uma conta de Diretório Ativo Azure válida que pretende fornecer nas caixas de texto relacionadas.

    b. Clique em **Guardar**.

    > [!NOTE]
    > O titular da conta Azure Ative Directory recebe um e-mail incluindo um link para confirmar a conta antes de se tornar ativa
    >  

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador freshService ou APIs fornecidas pela FreshService para fornecer contas de utilizador Azure AD.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Freshservice no Painel de Acesso, deverá ser automaticamente inscrito no Freshservice para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente freshservice com Azure AD](https://aad.portal.azure.com/)