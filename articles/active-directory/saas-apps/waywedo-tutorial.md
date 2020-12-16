---
title: 'Tutorial: Integração do Azure Ative Directory com a Forma como fazemos Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Way We Do.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: jeedes
ms.openlocfilehash: 0205617198ec8e57cb891d2415d2b5413a945f1b
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97608940"
---
# <a name="tutorial-integrate-way-we-do-with-azure-active-directory"></a>Tutorial: Integrar a forma de fazer com o Diretório Ativo Azure

Neste tutorial, você vai aprender a integrar o Caminho que fazemos com o Azure Ative Directory (Azure AD). Quando integras o Modo que fazemos com a AD Azure, podes:

* Controle em Azure AD que tem acesso ao Way We Do.
* Inseja os seus utilizadores para serem automaticamente inscritos na Way We Do com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode ter um mês de teste gratuito [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Forma de fazer um único sign-on (SSO) ativado subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Way We Do suporta **SSO** iniciado SP
* A forma como fazemos suporta o fornecimento do utilizador **just in time**

## <a name="adding-way-we-do-from-the-gallery"></a>Adicionando o caminho que fazemos da galeria

Para configurar a integração do Way We Do em AD Azure, precisa adicionar Way We Do da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **escreva o Caminho que fazemos** na caixa de pesquisa.
1. Selecione **Caminho que fazemos** a partir do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Configure e teste Azure AD SSO com a Way We Do usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Way We Do.

Para configurar e testar Azure AD SSO com a Way We Do, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure a forma como fazemos SSO](#configure-way-we-do-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Way We Do test user](#create-way-we-do-test-user)** - ter uma contraparte de Britta Simon no Caminho Que Fazemos que está ligada à representação AD Ad do utilizador.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Way We Do,** encontre a secção **Gerir** e selecione **'Único sinal de entrada'.**
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na página **de Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.waywedo.com/Authentication/ExternalSignIn`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.waywedo.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. [Contacte a equipa de apoio ao Cliente](mailto:support@waywedo.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** encontre **Certificado (Raw)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

   ![O link de descarregamento de certificado](common/certificateraw.png)

1. Na secção **set up Way We Do,** copie os URL(s) apropriados com base no seu requisito.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-way-we-do-sso"></a>Configurar forma de SSO

1. Para automatizar a configuração dentro da Forma como fazemos, tem de instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

1. Depois de adicionar extensão ao navegador, clique em **Setup Way We Do** irá direcioná-lo para a aplicação Way We Do. A partir daí, forneça as credenciais de administração para assinar no Caminho que Fazemos. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-6.

    ![Configuração de configuração](common/setup-sso.png)

1. Se pretender configurar a Way We Do manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa Way We Do como administrador e execute os seguintes passos:

1. Clique no **ícone da pessoa** no canto superior direito de qualquer página no Caminho Que Faça e, em seguida, clique em **'Conta'** no menu suspenso.

    ![Como fazemos conta](./media/waywedo-tutorial/tutorial_waywedo_account.png)

1. Clique no ícone do **menu** para abrir o menu de navegação push e clique **em Signo Único**.

    ![Way We Do single](./media/waywedo-tutorial/tutorial_waywedo_single.png)

1. Na página **de configuração de inscrição única,** execute os seguintes passos:

    ![Maneira de salvar](./media/waywedo-tutorial/tutorial_waywedo_save.png)

    1. Clique **na alternação de inscrição única** para **Sim** para ativar o Sign-On único.

    1. Na caixa de texto **de nome único,** insira o seu nome.

    1. Na **caixa de** texto ID da Entidade, cole o valor do **Identificador AD Azure,** que copiou a partir do portal Azure.

    1. Na caixa de texto **URL SSO SAML,** cole o valor do URL de **Login,** que copiou a partir do portal Azure.

    1. Faça o upload do certificado clicando no botão **selecionado** ao lado **do Certificado**.

    1. **Definições opcionais** -

        * Ativar palavras-passe - Quando esta opção é desativada, a palavra-passe regular funciona para que os utilizadores possam utilizar apenas o início de sção.

        * Ativar o provisionamento automático - Quando este estiver ativado, o endereço de e-mail utilizado para iniciar sing-on será automaticamente comparado com a lista de utilizadores na Forma como fazemos. Se o endereço de e-mail não corresponder a um utilizador ativo na Forma como fazemos, adiciona automaticamente uma nova conta de utilizador para a pessoa que se insere, solicitando qualquer informação em falta.

          > [!NOTE]
          > Os utilizadores adicionados através de um único sign-on são adicionados como utilizadores gerais e não são atribuídos um papel no sistema. Um administrador é capaz de entrar e modificar o seu papel de segurança como editor ou administrador e também pode atribuir uma ou várias funções de Org Chart.

    1. Clique **em Guardar** para persistir as suas definições.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Way We Do.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Way We Do**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="create-way-we-do-test-user"></a>Criar forma de testar o utilizador

Nesta secção, um utilizador chamado Britta Simon é criado no Way We Do. A Forma como fazemos suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Caminho Que Fazemos, um novo é criado após a autenticação.

> [!Note]
> Se precisar de criar um utilizador manualmente, contacte a [equipa de suporte do Cliente Way We Do Client](mailto:support@waywedo.com).

### <a name="test-sso"></a>Teste SSO

Quando selecionar o azulejo "Way We Do" no Painel de Acesso, deverá ser automaticamente inscrito na Forma como fazemos para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)