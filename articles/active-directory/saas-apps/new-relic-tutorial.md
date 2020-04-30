---
title: 'Tutorial: Integração do Diretório Ativo Azure com Nova Relíquia por Conta [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a New Relic por conta.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3186b9a8-f4d8-45e2-ad82-6275f95e7aa6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/16/2020
ms.author: jeedes
ms.openlocfilehash: 9fdcec5b55f52b7b6b824bf2ba25c14541b2a3c6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82186571"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic-by-account"></a>Tutorial: Azure Ative Diretório integração de um único sign-on (SSO) com nova relíquia por conta

Neste tutorial, você vai aprender a integrar A Nova Relíquia por Conta com o Azure Ative Directory (Azure AD). Quando integrar a Nova Relíquia por Conta com o Azure AD, pode:

* Controlo em Azure AD que tem acesso a New Relic por conta.
* Permita que os seus utilizadores sejam automaticamente inscritos na New Relic por conta com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Nova Relíquia por Conta única subscrição (SSO) ativada por subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Nova Relíquia por Conta suporta **SP** iniciado SSO

* Assim que configurar a Nova Relíquia por Conta, pode impor controlos de sessão, que protegem a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controlos de sessão estendem-se a partir do Acesso Condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

## <a name="adding-new-relic-by-account-from-the-gallery"></a>Adicionar Nova Relíquia por Conta da galeria

Para configurar a integração da Nova Relíquia por Conta em Azure AD, você precisa adicionar New Relic por Conta da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, digite **New Relic por Conta** na caixa de pesquisa.
1. Selecione **New Relic por Conta** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-new-relic-by-account"></a>Configure e teste Azure AD único sign-on para Nova Relíquia por Conta

Configure e teste Azure AD SSO com Nova Relíquia por Conta utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em New Relic por conta.

Para configurar e testar o Azure AD SSO com a New Relic por conta, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure a Nova Relíquia por SSO](#configure-new-relic-by-account-sso)** de Conta - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Criar nova Relíquia por utilizador](#create-new-relic-by-account-test-user)** de teste de conta - para ter uma contrapartida de B.Simon em New Relic por Conta que esteja ligada à representação da AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **da Nova Relíquia por Conta,** encontre a secção **Gerir** e selecione um **único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)
1. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:

    `https://rpm.newrelic.com/accounts/{acc_id}/sso/saml/login`- Certifique-se `acc_id` de substituir a sua própria identificação de conta da New Relic por conta.

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL:`rpm.newrelic.com`

1. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção **Configurar Nova Relíquia por Conta,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de anúncio sinuoso

    c. Logout URL

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

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso à New Relic por conta.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **New Relic por Conta**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-new-relic-by-account-sso"></a>Configure nova relíquia por Conta SSO

1. Numa janela diferente do navegador web, inscreva-se no site da empresa **New Relic by Account** como administrador.

2. No menu em cima, clique em **Definições de Conta**.
   
    ![Definições de conta](./media/new-relic-tutorial/ic797036.png "Definições de conta")

3. Clique no separador **De Segurança e autenticação** e, em seguida, clique **no sinal único no** separador.
   
    ![Inscrição única](./media/new-relic-tutorial/ic797037.png "Início de Sessão Único")

4. Na página de diálogo SAML, execute os seguintes passos:
   
    ![SAML](./media/new-relic-tutorial/ic797038.png "SAML")
   
    a. Clique **em Escolher Ficheiro** para fazer o upload do certificado de Diretório Ativo Azure descarregado.

    b. Na caixa de texto url de **login remoto,** colá o valor do URL de **Login,** que copiou do portal Azure.
   
    c. Na caixa de texto url de **aterragem de logout,** colá o valor do URL de **Logout,** que copiou do portal Azure.

    d. Clique em **Guardar as minhas alterações**.

### <a name="create-new-relic-by-account-test-user"></a>Criar nova relíquia por utilizador de teste de conta

1. Assine a sua **Nova Relíquia por Conta** como administrador.

2. No menu em cima, clique em **Definições de Conta**.
   
    ![Definições de conta](./media/new-relic-tutorial/ic797040.png "Definições de conta")

3. No painel **da Conta** no lado esquerdo, clique em **Resumo**, e, em seguida, clique em **Adicionar utilizador**.
   
    ![Definições de conta](./media/new-relic-tutorial/ic797041.png "Definições de conta")

4. No diálogo dos **utilizadores Ativos,** execute os seguintes passos:
   
    ![Utilizadores Ativos](./media/new-relic-tutorial/ic797042.png "Utilizadores Ativos")
   
    a. Na caixa de texto **por e-mail,** escreva o endereço de e-mail de um utilizador de Diretório Ativo Azure válido que pretende fornecer.

    b. Como **Função** selecione **Utilizador**.

    c. Clique em **Adicionar este utilizador**.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador new relic por conta ou APIs fornecidas pela New Relic por Conta para fornecer contas de utilizador Azure AD.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar na nova relíquia por conta no Painel de Acesso, deve ser automaticamente inscrito na Nova Relíquia por Conta para a qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente nova relíquia por conta com AD Azure](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)