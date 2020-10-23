---
title: 'Tutorial: Integração do Azure Ative Directory com o JOBHUB Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o JOBHUB.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/16/2019
ms.author: jeedes
ms.openlocfilehash: 276d444239cb91f1bec2975506279721e6d0008c
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92459361"
---
# <a name="tutorial-integrate-jobhub-with-azure-active-directory"></a>Tutorial: Integrar JOBHUB com Diretório Ativo Azure

Neste tutorial, você vai aprender a integrar JOBHUB com Azure Ative Directory (Azure AD). Quando integrar o JOBHUB com a AD Azure, pode:

* Controlo em Azure AD que tem acesso ao JOBHUB.
* Permita que os seus utilizadores sejam automaticamente inscritos no JOBHUB com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode ter um mês de teste gratuito [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura ativada por assinatura única JOBHUB (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste. JOBHUB apoia sSO iniciado **SP.**

## <a name="adding-jobhub-from-the-gallery"></a>Adicionar JOBHUB da galeria

Para configurar a integração do JOBHUB no Azure AD, precisa adicionar JOBHUB da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** escreva **JOBHUB** na caixa de pesquisa.
1. Selecione **JOBHUB** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Configure e teste Azure AD SSO com JOBHUB usando um utilizador de teste chamado **Britta Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no JOBHUB.

Para configurar e testar o Azure AD SSO com o JOBHUB, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure JOBHUB SSO](#configure-jobhub-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create JOBHUB test user](#create-jobhub-test-user)** - ter uma contraparte de Britta Simon no JOBHUB que está ligada à representação AD AD do utilizador.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **JOBHUB,** encontre a secção **Gerir** e selecione **'Único sinal de s-on'.**
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** introduza os valores para os seguintes campos: Na caixa de texto **URL sign-on,** digite um URL utilizando o seguinte padrão:  `https://pasona.jobhub.jp/saml/init`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de Sign-On real. Contacte [a equipa de suporte do cliente JOBHUB](mailto:platform@pasonagroup.co.jp) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na secção **Certificado de Assinatura SAML,** clique em Editar o botão **Editar** para abrir o diálogo **do Certificado de Assinatura SAML.**

    ![Editar certificado de assinatura SAML](common/edit-certificate.png)

1. Na secção **certificado de assinatura SAML,** copie a **impressão digital e** guarde-a no seu computador.

    ![Valor da impressão digital do polegar da cópia](common/copy-thumbprint.png)

1. Na secção **Configurar JOBHUB,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-jobhub-sso"></a>Configurar JOBHUB SSO

Para configurar um único sign-on no lado **JOBHUB,** você precisa enviar o **valor de impressão digital thumbprint** e URLs copiados apropriados do portal Azure para a equipe de suporte [JOBHUB](mailto:platform@pasonagroup.co.jp). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

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

Nesta secção, você permitirá que Britta Simon use a Azure single sign-on, concedendo acesso ao JOBHUB.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **JOBHUB**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **Britta Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="create-jobhub-test-user"></a>Criar utilizador de teste JOBHUB

Nesta secção, cria-se um utilizador chamado Britta Simon no JOBHUB. Trabalhe com a [equipa de apoio jobhub](mailto:platform@pasonagroup.co.jp) para adicionar os utilizadores na plataforma JOBHUB. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

### <a name="test-sso"></a>Teste SSO

Quando selecionar o azulejo JOBHUB no Painel de Acesso, deverá ser automaticamente inscrito no JOBHUB para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)