---
title: 'Tutorial: Integração do Azure Ative Directory com o MyWorkDrive Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o MyWorkDrive.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/27/2019
ms.author: jeedes
ms.openlocfilehash: aa0fe40ec28aea7f82ee0e635b3c42140a472816
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88552446"
---
# <a name="tutorial-integrate-myworkdrive-with-azure-active-directory"></a>Tutorial: Integrar o MyWorkDrive com o Azure Ative Directory

Neste tutorial, você vai aprender a integrar o MyWorkDrive com o Azure Ative Directory (Azure AD). Quando integrar o MyWorkDrive com AD Azure, pode:

* Controle em Azure AD que tem acesso ao MyWorkDrive.
* Ativar os seus utilizadores a serem automaticamente inscritos no MyWorkDrive com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode ter um mês de teste gratuito [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Subscrição ativada pelo MyWorkDrive (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste. MyWorkDrive suporta **SSO** iniciado sp e **IDP**

## <a name="adding-myworkdrive-from-the-gallery"></a>Adicionar MyWorkDrive da galeria

Para configurar a integração do MyWorkDrive no AD Azure, é necessário adicionar o MyWorkDrive da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** digite **MyWorkDrive** na caixa de pesquisa.
1. Selecione **MyWorkDrive** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Configure e teste Azure AD SSO com MyWorkDrive usando um utilizador de teste chamado **Britta Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no MyWorkDrive.

Para configurar e testar o Azure AD SSO com o MyWorkDrive, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure myWorkDrive SSO](#configure-myworkdrive-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create MyWorkDrive test user](#create-myworkdrive-test-user)** - para ter uma contraparte de Britta Simon no MyWorkDrive que está ligada à representação AD AD do utilizador.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **MyWorkDrive,** encontre a secção **Gerir** e selecione **Single sign-on**.
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na página **de Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para o seguinte campo:

    Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:  `https://<SERVER.DOMAIN.COM>/SAML/AssertionConsumerService.aspx`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<SERVER.DOMAIN.COM>/Account/Login-saml`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de resposta real e Sign-On URL. Insira o nome de anfitrião do MyWorkDrive Server da sua própria empresa: por exemplo.
    > 
    > URL de resposta: `https://yourserver.yourdomain.com/SAML/AssertionConsumerService.aspx`
    > 
    > URL de inscrição:`https://yourserver.yourdomain.com/Account/Login-saml`
    > 
    > Contacte a [equipa de suporte do MyWorkDrive](mailto:support@myworkdrive.com) se não tiver a certeza de como configurar o seu próprio nome de anfitrião e certificado TLS/SSL para estes valores.

1. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique no botão de cópia para copiar o **Url de metadados da Federação de Aplicações** para a sua área de transferência.

    ![O link de descarregamento de certificado](common/copy-metadataurl.png)

### <a name="configure-myworkdrive-sso"></a>Configure MyWorkDrive SSO

1. Para automatizar a configuração dentro do MyWorkDrive, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

1. Depois de adicionar extensão ao navegador, clique em **Setup MyWorkDrive** irá direcioná-lo para a aplicação MyWorkDrive. A partir daí, forneça as credenciais de administração para assinar no MyWorkDrive. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-4.

    ![Configuração de configuração](common/setup-sso.png)

1. Se pretender configurar o MyWorkDrive manualmente, numa janela diferente do navegador web, inscreva-se no MyWorkDrive como Administrador de Segurança.

1. No MyWorkDrive Server no painel de administração, clique na **ENTERPRISE** e execute os seguintes passos:

    ![O Administrador](./media/myworkdrive-tutorial/tutorial_myworkdrive_admin.png)

    a. Ativar **a SSO SAML/ADFS**.

    b. Selecione **SAML - Ad Azure**

    c. Na caixa de texto **do Url textbox da Federação de Metadados da Azure App,** cole o valor do Url de **Metadados da Federação** de Aplicações que copiou do portal Azure.

    d. Clicar em **Guardar**

    > [!NOTE]
    > Para obter informações adicionais, reveja o [artigo de suporte da MyWorkDrive Azure AD](https://www.myworkdrive.com/support/saml-single-sign-on-azure-ad/).

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado Britta Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `Britta Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `BrittaSimon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que Britta Simon use a Azure single sign-on, concedendo acesso ao MyWorkDrive.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **MyWorkDrive**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **Britta Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="create-myworkdrive-test-user"></a>Criar utilizador de teste MyWorkDrive

Nesta secção, cria-se um utilizador chamado Britta Simon no MyWorkDrive. Trabalhe com a [equipa de suporte MyWorkDrive](mailto:support@myworkdrive.com) para adicionar os utilizadores na plataforma MyWorkDrive. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

### <a name="test-sso"></a>Teste SSO

Quando selecionar o azulejo MyWorkDrive no Painel de Acesso, deverá ser automaticamente inscrito no MyWorkDrive para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)