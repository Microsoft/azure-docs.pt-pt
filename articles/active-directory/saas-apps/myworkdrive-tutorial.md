---
title: 'Tutorial: Integração de Diretório Ativo Azure com o MyWorkDrive [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o MyWorkDrive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 4d049778-3c7b-46c0-92a4-f2633a32334b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/27/2019
ms.author: jeedes
ms.openlocfilehash: 60fdd9b0a8fb272da885df97e39804a98e48de67
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80478864"
---
# <a name="tutorial-integrate-myworkdrive-with-azure-active-directory"></a>Tutorial: Integrar o MyWorkDrive com o Diretório Ativo Azure

Neste tutorial, aprenderá a integrar o MyWorkDrive com o Azure Ative Directory (Azure AD). Quando integrar o MyWorkDrive com o Azure AD, pode:

* Controle em Azure AD que tem acesso ao MyWorkDrive.
* Ative que os seus utilizadores sejam automaticamente inscritos no MyWorkDrive com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode ter um mês de experiência gratuita [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* MyWorkDrive single sign-on (SSO) enabled subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste. MyWorkDrive suporta **SP** e **IDP** iniciadoS SSO

## <a name="adding-myworkdrive-from-the-gallery"></a>Adicionando MyWorkDrive da galeria

Para configurar a integração do MyWorkDrive em Azure AD, precisa de adicionar o MyWorkDrive da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** **digite MyWorkDrive** na caixa de pesquisa.
1. Selecione **MyWorkDrive** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Configure e teste Azure AD SSO com MyWorkDrive utilizando um utilizador de teste chamado **Britta Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no MyWorkDrive.

Para configurar e testar o Azure AD SSO com o MyWorkDrive, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure myWorkDrive SSO](#configure-myworkdrive-sso)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Create MyWorkDrive test user](#create-myworkdrive-test-user)** - para ter uma contrapartida de Britta Simon no MyWorkDrive que está ligada à representação azure AD do utilizador.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **MyWorkDrive,** encontre a secção **Gerir** e selecione **um único sinal .**
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na página **basic SAML Configuração,** Se desejar configurar a aplicação no modo iniciado **idp,** introduza os valores para o seguinte campo:

    Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<SERVER.DOMAIN.COM>/SAML/AssertionConsumerService.aspx`

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<SERVER.DOMAIN.COM>/Account/Login-saml`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de Resposta real e URL de Sinal. Insera o nome de anfitrião do MyWorkDrive Server da sua própria empresa:por exemplo.
    > 
    > URL de resposta: `https://yourserver.yourdomain.com/SAML/AssertionConsumerService.aspx`
    > 
    > URL de inscrição:`https://yourserver.yourdomain.com/Account/Login-saml`
    > 
    > Contacte a equipa de [suporte MyWorkDrive](mailto:support@myworkdrive.com) se não tiver a certeza de como configurar o seu próprio nome de anfitrião e o certificado TLS/SSL para estes valores.

1. Na configuração do Single Sign-On com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique no botão de cópia para copiar o Url de **Metadados da Federação** da Aplicação para a sua área de recção.

    ![O link de descarregamento do Certificado](common/copy-metadataurl.png)

### <a name="configure-myworkdrive-sso"></a>Configure MyWorkDrive SSO

1. Para automatizar a configuração dentro do MyWorkDrive, precisa de instalar a extensão de **'Sign-in' de Aplicações Seguras,** clicando em **instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

1. Depois de adicionar extensão ao navegador, clique em **Configurar o MyWorkDrive** irá direcioná-lo para a aplicação MyWorkDrive. A partir daí, forneça as credenciais de administração para assinar no MyWorkDrive. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-4.

    ![Configuração de configuração de configuração](common/setup-sso.png)

1. Se quiser configurar o MyWorkDrive manualmente, numa janela de navegador web diferente, inscreva-se no MyWorkDrive como Administrador de Segurança.

1. No MyWorkDrive Server no painel de administração, clique no **ENTERPRISE** e execute os seguintes passos:

    ![O Administrador](./media/myworkdrive-tutorial/tutorial_myworkdrive_admin.png)

    a. Ativar **saml/aDFS SSO**.

    b. Selecione **SAML - Azure AD**

    c. Na caixa de texto url da Federação de Metadados da **App Azure,** colá-cola o valor do Url de Metadados da Federação de **Aplicações** que copiou do portal Azure.

    d. Clique em **Guardar**

    > [!NOTE]
    > Para mais informações, reveja o artigo de [suporte da MyWorkDrive Azure AD](https://www.myworkdrive.com/support/saml-single-sign-on-azure-ad/).

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado Britta Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `Britta Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `BrittaSimon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que Britta Simon utilize um único sign-on Azure, concedendo acesso ao MyWorkDrive.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **MyWorkDrive**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **Britta Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

### <a name="create-myworkdrive-test-user"></a>Criar o utilizador de teste MyWorkDrive

Nesta secção, cria-se uma utilizadora chamada Britta Simon no MyWorkDrive. Trabalhe com a equipa de [suporte MyWorkDrive](mailto:support@myworkdrive.com) para adicionar os utilizadores na plataforma MyWorkDrive. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

### <a name="test-sso"></a>Teste SSO

Quando selecionar o azulejo MyWorkDrive no Painel de Acesso, deve ser automaticamente inscrito no MyWorkDrive para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)