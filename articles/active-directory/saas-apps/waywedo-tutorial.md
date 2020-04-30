---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Way We Do [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Diretório Ativo Azure e o Way We Do.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 84fc4f36-ecd1-42c6-8a70-cb0f3dc15655
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: faa23f61e5a213c492a7fb51bfc5b108e5c77946
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "67310409"
---
# <a name="tutorial-integrate-way-we-do-with-azure-active-directory"></a>Tutorial: Integrar a forma como fazemos com o Diretório Ativo Azure

Neste tutorial, você vai aprender a integrar Way We Do with Azure Ative Directory (Azure AD). Quando integrar o Way We Do with Azure AD, pode:

* Controlo em Azure AD que tem acesso à Way We Do.
* Permitir que os seus utilizadores sejam automaticamente inscritos na Way We Do com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode ter um mês de experiência gratuita [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Way We Do single sign-on (SSO) enabled subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Way We Do apoia **SP** iniciado SSO
* Way We Do suporta o fornecimento de utilizadores **justo no tempo**

## <a name="adding-way-we-do-from-the-gallery"></a>Adicionando maneira que fazemos da galeria

Para configurar a integração do Way We Do in Azure AD, você precisa adicionar Way We Do da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** escreva **Como Fazemos** na caixa de pesquisa.
1. Selecione **Way We Do** from results panel and then add the app. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Configure e teste Azure AD SSO com way we do usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na Way We Do.

Para configurar e testar o Azure AD SSO com a Way We Do, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure way We Do SSO](#configure-way-we-do-sso)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Create Way We Do test user](#create-way-we-do-test-user)** - para ter uma contrapartida de Britta Simon em Way We Do que está ligada à representação do utilizador da AD Azure.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Way We Do,** encontre a secção **Gerir** e selecione **single sign-on**.
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na página **basic SAML Configuração,** introduza os valores para os seguintes campos:

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<SUBDOMAIN>.waywedo.com/Authentication/ExternalSignIn`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`https://<SUBDOMAIN>.waywedo.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e identificador. Contacte [a equipa de suporte](mailto:support@waywedo.com) do Cliente para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Na configuração de um único sign-on com a página **SAML,** na secção certificado de **assinatura SAML,** encontre **certificado (Cru)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

   ![O link de descarregamento do Certificado](common/certificateraw.png)

1. Na secção **Configurar A Forma de Fazer,** copie os URL(s) adequados com base no seu requisito.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-way-we-do-sso"></a>Configure forma de fazer SSO

1. Para automatizar a configuração dentro do Modo de Fazer, precisa de instalar a extensão de **navegador Secure-in das Minhas Aplicações** clicando em **instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

1. Depois de adicionar extensão ao navegador, clique na **Configuração Way We Do** irá direcioná-lo para a aplicação Way We Do. A partir daí, forneça as credenciais de administração para assinar na Way We Do. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-6.

    ![Configuração de configuração de configuração](common/setup-sso.png)

1. Se quiser configurar o Way We Do manualmente, abra uma nova janela do navegador web e instique-se no site da empresa Way We Do como administrador e execute os seguintes passos:

1. Clique no **ícone da pessoa** no canto superior direito de qualquer página da Maneira Que Fazemos e, em seguida, clique em **Conta** no menu dropdown.

    ![Maneira como fazemos conta](./media/waywedo-tutorial/tutorial_waywedo_account.png)

1. Clique no **ícone** do menu para abrir o menu de navegação push e clique **em sinal único .**

    ![Maneira que fazemos solteiro](./media/waywedo-tutorial/tutorial_waywedo_single.png)

1. Na página de configuração de início de **sessão individual,** execute os seguintes passos:

    ![Maneira que nós fazemos salvar](./media/waywedo-tutorial/tutorial_waywedo_save.png)

    a. Clique **no alternância** de inscrição única para **Sim** para ativar o Single Sign-On.

    b. Na caixa de texto de **nome de assinatura única,** introduza o seu nome.

    c. Na caixa de texto **Id da Entidade,** colhe o valor do **Identificador Azure AD,** que copiou do portal Azure.

    d. Na caixa de texto **URL SAML SSO,** colá o valor do URL de **Login,** que copiou do portal Azure.

    e. Faça upload do certificado clicando no botão **select** o lado do **Certificado**.

    f. **Definições Opcionais** -
    
    * Ativar palavras-passe - Quando esta opção é desativada, a palavra-passe regular funciona para a forma como fazemos para que os utilizadores possam utilizar apenas um único sinal.

    * Ativar o fornecimento automático - Quando isto estiver ativado, o endereço de e-mail utilizado para iniciar sessão será automaticamente comparado com a lista de utilizadores da Way We Do. Se o endereço de e-mail não corresponder a um utilizador ativo na Forma como fazemos, adiciona automaticamente uma nova conta de utilizador para a pessoa que faz o instituto, solicitando qualquer informação em falta.

      > [!NOTE]
      > Os utilizadores adicionados através de um único sign-on são adicionados como utilizadores gerais e não são atribuídos uma função no sistema. Um Administrador é capaz de entrar e modificar o seu papel de segurança como editor ou administrador e também pode atribuir uma ou várias funções de Org Chart.

    g. Clique em **Guardar** para persistir nas definições.

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

Nesta secção, você permitirá que B.Simon use o único sign-on Azure, concedendo acesso ao Way We Do.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Way We Do**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

### <a name="create-way-we-do-test-user"></a>Criar modo como fazemos o utilizador de teste

Nesta secção, um utilizador chamado Britta Simon é criado em Way We Do. A Forma como fazemos suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir na Way We Do, um novo é criado após a autenticação.

> [!Note]
> Se precisar de criar um utilizador manualmente, contacte a equipa de suporte ao [Cliente Way We Do](mailto:support@waywedo.com)Client .

### <a name="test-sso"></a>Teste SSO

Quando selecionar o azulejo Way We Do no Painel de Acesso, deve ser automaticamente inscrito na forma como fazemos para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)