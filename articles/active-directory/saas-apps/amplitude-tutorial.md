---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com Amplitude / Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Amplitude.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.openlocfilehash: b05a85dd61cdf4edc366edc3fbf20e4097308d09
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91713570"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-amplitude"></a>Tutorial: Azure Ative Directy integração única (SSO) com amplitude

Neste tutorial, você vai aprender a integrar amplitude com Azure Ative Direy (Azure AD). Quando integra a Amplitude com a AD Azure, pode:

* Controlo em Azure AD que tem acesso à Amplitude.
* Ative os seus utilizadores a serem automaticamente inscritos na Amplitude com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura única de amplitude (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Amplitude apoia **SP e IDP** iniciado SSO
* Amplitude suporta **provisão do** utilizador Just In Time

## <a name="adding-amplitude-from-the-gallery"></a>Adicionar Amplitude da galeria

Para configurar a integração da Amplitude no AD Azure, é necessário adicionar amplitude da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **escreva Amplitude** na caixa de pesquisa.
1. Selecione **Amplitude** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-amplitude"></a>Configurar e testar Azure AD único sinal para amplitude

Configure e teste Azure AD SSO com amplitude usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Amplitude.

Para configurar e testar a Azure AD SSO com amplitude, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure Amplitude SSO](#configure-amplitude-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create Amplitude test user](#create-amplitude-test-user)** - ter uma contraparte de B.Simon in Amplitude que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração da aplicação **Amplitude,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **identifier,** digite um URL: `https://amplitude.com/saml/sso/metadata`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://analytics.amplitude.com/saml/sso/<uniqueid>`

    > [!NOTE]
    > O valor URL de resposta não é real. Você receberá o valor URL de resposta mais tarde neste tutorial.

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL:  `https://analytics.amplitude.com/sso`

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configuração amplitude,** copie os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Amplitude.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Amplitude**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-amplitude-sso"></a>Configurar amplitude SSO

1. Para automatizar a configuração dentro da Amplitude, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

1. Depois de adicionar extensão ao navegador, clique em **Configurar Amplitude** irá direcioná-lo para a aplicação Amplitude. A partir daí, forneça as credenciais de administração para assinar na Amplitude. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-6.

    ![Configuração de configuração](common/setup-sso.png)

1. Se pretender configurar manualmente a Amplitude, abra uma nova janela do navegador web e inscreva-se no site da empresa Amplitude como administrador e execute os seguintes passos:

1. Clique no **Administrador plano** a partir da barra de navegação esquerda.

    ![O screenshot mostra o menu Amplitude com o Plan Admin selecionado.](./media/amplitude-tutorial/configure1.png)

1. Selecione **os metadados do Diretório Ativo Microsoft Azure** da **Integração SSO**.

    ![O Screenshot mostra o painel de Admin do Plano com o link de metadados do Diretório Ativo microsoft Azure chamado.](./media/amplitude-tutorial/configure2.png)

1. Na secção **set Up Single Sign-On,** execute os seguintes passos:

    ![A screenshot mostra a secção de inscrição única com valores descritos neste passo.](./media/amplitude-tutorial/configure3.png)

    a. Abra o **Metdata Xml** descarregado do portal Azure no bloco de notas, cole o conteúdo na caixa de texto do **Microsoft Azure Ative Metadata.**

    b. Copie o valor **URL de resposta (ACS)** e cole-o na caixa de texto URL de **resposta** da **Configuração Básica SAML** no portal Azure.

    c. Clicar em **Guardar**

### <a name="create-amplitude-test-user"></a>Criar utilizador de teste de amplitude

Nesta secção, um utilizador chamado B.Simon é criado em Amplitude. A Amplitude suporta o provisionamento do utilizador just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir na Amplitude, um novo é criado após a autenticação.

> [!Note]
> Se necessitar de criar um utilizador manualmente, contacte a [equipa de suporte da Amplitude](https://amplitude.zendesk.com).

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo amplitude no Painel de Acesso, deverá ser automaticamente inscrito na Amplitude para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente amplitude com Azure AD](https://aad.portal.azure.com/)