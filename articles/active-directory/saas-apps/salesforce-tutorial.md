---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com a Salesforce  Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/17/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a875cee7e6796a2c865bde4a62f2f0463eb12130
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "78967712"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce"></a>Tutorial: Azure Ative Directory integração individual (SSO) com a Salesforce

Neste tutorial, você vai aprender a integrar a Salesforce com o Azure Ative Directory (Azure AD). Quando integrar a Salesforce com a Azure AD, pode:

* Controlo em Azure AD que tem acesso à Salesforce.
* Ative que os seus utilizadores sejam automaticamente inscritos na Salesforce com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura ativada por um único sinal (SSO) da Salesforce.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Salesforce apoia **SP** iniciado SSO

* A Salesforce suporta [o fornecimento e o deprovisionamento **automatizado** ](salesforce-provisioning-tutorial.md) de utilizadores (recomendado)

* Salesforce suporta o provisionamento de utilizadores **justo no tempo**

* A aplicação Salesforce Mobile pode agora ser configurada com AD Azure para ativar o SSO. Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.
* Assim que configurar a Salesforce, pode impor o Controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Controlo de Sessão estende-se a partir de Acesso Condicional. [Saiba como impor o controlo de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-salesforce-from-the-gallery"></a>Adicionando Salesforce da galeria

Para configurar a integração da Salesforce em Azure AD, precisa adicionar a Salesforce da galeria à sua lista de aplicações geridas do SaaS.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **Salesforce** na caixa de pesquisa.
1. Selecione **Salesforce** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-salesforce"></a>Configure e teste Azure AD única inscrição para Salesforce

Configure e teste Azure AD SSO com Salesforce utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Salesforce.

Para configurar e testar o Azure AD SSO com a Salesforce, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure salesforce SSO](#configure-salesforce-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * Criar o utilizador de **[teste Salesforce](#create-salesforce-test-user)** - para ter uma contrapartida da B.Simon na Salesforce que esteja ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configure Azure AD SSO

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Salesforce,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **smS sign-on** URL, escreva o valor utilizando o seguinte padrão:

    Conta empresarial: `https://<subdomain>.my.salesforce.com`

    Conta de desenvolvimento: `https://<subdomain>-dev-ed.my.salesforce.com`

    b. Na caixa de texto **identificador,** digite o valor utilizando o seguinte padrão:

    Conta empresarial: `https://<subdomain>.my.salesforce.com`

    Conta de desenvolvimento: `https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL e Identificador de Sinal real. Contacte a equipa de suporte ao [Cliente salesforce](https://help.salesforce.com/support) para obter estes valores.

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **metadados da Federação XML** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Na secção **"set up Salesforce",** copie os URL(s) adequados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **de nome do utilizador,** introduza o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, permitirá que a B.Simon utilize um único sign-on azure, concedendo acesso à Salesforce.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Salesforce**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-salesforce-sso"></a>Configure Salesforce SSO

1. Para automatizar a configuração dentro do Salesforce, precisa de instalar a extensão de **navegador Secure-in das Minhas Aplicações** clicando em **instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

1. Depois de adicionar extensão ao navegador, clique em **Configurar a Salesforce** irá direcioná-lo para a aplicação Salesforce Single Sign-On. A partir daí, forneça as credenciais de administração para assinar no Salesforce Single Sign-On. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-13.

    ![Configuração de configuração de configuração](common/setup-sso.png)

1. Se pretender configurar o Salesforce manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa Salesforce como administrador e execute os seguintes passos:

1. Clique no ícone **configuração** no **canto** superior direito da página.

    ![Configurar o início de sessão único](./media/salesforce-tutorial/configure1.png)

1. Desloque-se até às **DEFINIÇÕES** no painel de navegação, clique em **Identidade** para expandir a secção relacionada. Em seguida, clique em **Definições de Sinal Único**.

    ![Configurar o início de sessão único](./media/salesforce-tutorial/sf-admin-sso.png)

1. Na página **De definições de sinal único,** clique no botão **Editar.**

    ![Configurar o início de sessão único](./media/salesforce-tutorial/sf-admin-sso-edit.png)

    > [!NOTE]
    > Se não conseguir ativar as definições de Sign-On single para a sua conta Salesforce, poderá ter de contactar a equipa de suporte ao [Cliente da Salesforce](https://help.salesforce.com/support).

1. Selecione **SAML Ativado**, e, em seguida, clique em **Guardar**.

    ![Configurar o início de sessão único](./media/salesforce-tutorial/sf-enable-saml.png)

1. Para configurar as definições de inscrição única sAML, clique em **Novo ficheiro de Metadados**.

    ![Configurar o início de sessão único](./media/salesforce-tutorial/sf-admin-sso-new.png)

1. Clique **em Escolher Ficheiro** para carregar o ficheiro XML de metadados que descarregou a partir do portal Azure e clique em **Criar**.

    ![Configurar o início de sessão único](./media/salesforce-tutorial/xmlchoose.png)

1. Na página **de Definições de Sinais Únicos SAML,** os campos povoam-se automaticamente, selecionam o **Fornecimento de Utilizador Ativado** e, em seguida, clique em **Guardar**.

    ![Configurar o início de sessão único](./media/salesforce-tutorial/salesforcexml.png)

1. No painel de navegação à esquerda no Salesforce, clique em **Definições da Empresa** para expandir a secção relacionada e, em seguida, clique no **Meu Domínio**.

    ![Configurar o início de sessão único](./media/salesforce-tutorial/sf-my-domain.png)

1. Desloque-se até à secção de **Configuração de Autenticação** e clique no botão **Editar.**

    ![Configurar o início de sessão único](./media/salesforce-tutorial/sf-edit-auth-config.png)

1. Na secção de **Configuração de Autenticação,** verifique o **AzureSSO** como Serviço de **Autenticação** da sua configuração SAML SSO e, em seguida, clique em **Guardar**.

    ![Configurar o início de sessão único](./media/salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > Se for selecionado mais de um serviço de autenticação, os utilizadores são solicitados a selecionar em que serviço de autenticação gostam de iniciar o início do início do seu ambiente Salesforce. Se não quer que aconteça, então deve deixar todos os outros serviços de **autenticação sem controlo.**

### <a name="create-salesforce-test-user"></a>Criar o utilizador de teste Salesforce

Nesta secção, um utilizador chamado B.Simon é criado na Salesforce. A Salesforce suporta o fornecimento just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Salesforce, um novo é criado quando se tenta aceder ao Salesforce. A Salesforce também suporta o fornecimento automático de utilizadores, pode encontrar mais detalhes [aqui](salesforce-provisioning-tutorial.md) sobre como configurar o fornecimento automático de utilizadores.

## <a name="test-sso"></a>Teste SSO

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clicar no azulejo Salesforce no Painel de Acesso, deverá ser automaticamente inscrito na Salesforce para a qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="test-sso-for-salesforce-mobile"></a>Teste SSO para Salesforce (Mobile)

1. Aplicação móvel Open Salesforce. Na página de sinal de inscrição, clique em **Use Custom Domain**.

    ![Aplicação móvel Salesforce](media/salesforce-tutorial/mobile-app1.png)

1. Na caixa de texto **de domínio personalizado,** introduza o nome de domínio personalizado registado e clique em **Continuar**.

    ![Aplicação móvel Salesforce](media/salesforce-tutorial/mobile-app2.png)

1. Insira as suas credenciais De DA Azure para iniciar sessão na aplicação Salesforce e clique **em Next**.

    ![Aplicação móvel Salesforce](media/salesforce-tutorial/mobile-app3.png)

1. Na página **Permitir o Acesso,** como mostrado abaixo, clique **em Permitir** dar acesso à aplicação Salesforce.

    ![Aplicação móvel Salesforce](media/salesforce-tutorial/mobile-app4.png)

1. Finalmente, após o início do início do sessão, a página inicial da aplicação será exibida.

    aplicação móvel ![Salesforce](media/salesforce-tutorial/mobile-app5.png) ![](media/salesforce-tutorial/mobile-app6.png) de aplicação móvel da Salesforce

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configurar o fornecimento de utilizadores](salesforce-provisioning-tutorial.md)

- [Experimente a Salesforce com a Azure AD](https://aad.portal.azure.com)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger a Salesforce com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/protect-salesforce)