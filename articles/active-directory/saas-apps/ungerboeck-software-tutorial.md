---
title: 'Tutorial: Integração do Azure Ative Directory com | de Software Ungerboeck Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Ungerboeck Software.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jeedes
ms.openlocfilehash: ddc83da2836d414ee3fadd23288475a6829018aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92521791"
---
# <a name="tutorial-integrate-ungerboeck-software-with-azure-active-directory"></a>Tutorial: Integrar software ungerboeck com diretório ativo Azure

Neste tutorial, você vai aprender a integrar o Software Ungerboeck com o Azure Ative Directory (Azure AD). Quando integrar o Software Ungerboeck com AZure AD, pode:

* Controle em Azure AD que tem acesso a Software Ungerboeck.
* Capacitar os seus utilizadores a serem automaticamente inscritos no Software Ungerboeck com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode ter um mês de teste gratuito [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Ungerboeck Software única subscrição ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste. Ungerboeck Software suporta SSO iniciado **SP.**

## <a name="adding-ungerboeck-software-from-the-gallery"></a>Adicionar Software Ungerboeck da galeria

Para configurar a integração do Software Ungerboeck no AD Azure, é necessário adicionar software Ungerboeck da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite Software Ungerboeck** na caixa de pesquisa.
1. Selecione **Ungerboeck Software** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Configure e teste Azure AD SSO com Software Ungerboeck usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Software Ungerboeck.

Para configurar e testar o Azure AD SSO com software Ungerboeck, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Software SSO descodeis](#configure-ungerboeck-software-sso)** para configurar as definições SSO no lado da aplicação.
3. **[Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user)** para testar o Azure AD com B.Simon.
4. **[Atribua ao utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que a B.Simon utilize um único sinal de Ad AD.
5. Crie o utilizador de **[teste de Software Ungerboeck](#create-ungerboeck-software-test-user)** para ter uma contraparte de B.Simon em Software Ungerboeck que está ligada à representação AD AD do utilizador.
6. **[Teste SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **do Software Ungerboeck,** encontre a secção **Gerir** e selecione **'Único sinal de sção'**.
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na página **de Configuração Básica SAML,** insira os valores para os seguintes campos:

    1. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.ungerboeck.com/prod`

    1. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão:
    
    *  **Para o ambiente de produção:**

       - `https://<SUBDOMAIN>.ungerboeck.com/prod`
       - `https://<SUBDOMAIN>.ungerboeck.net/prod`
       - `https://<SUBDOMAIN>.ungerboeck.io/prod`

   * **Para ambiente de ensaio:**

     - `https://<SUBDOMAIN>.ungerboeck.com/test`
     - `https://<SUBDOMAIN>.ungerboeck.net/test`
     - `https://<SUBDOMAIN>.ungerboeck.io/test`

   > [!NOTE]
   > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador, que é explicado mais tarde na secção **Configure Ungerboeck Software Single Sign-On** do tutorial.

1. Na secção **Certificado de Assinatura SAML,** clique em Editar o botão **Editar** para abrir o diálogo **do Certificado de Assinatura SAML.**

    ![Editar certificado de assinatura SAML](common/edit-certificate.png)

1. Na secção **certificado de assinatura SAML,** copie a **impressão digital e** guarde-a no seu computador.

    ![Valor da impressão digital do polegar da cópia](common/copy-thumbprint.png)

1. Na secção **De Software Ungerboeck,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-ungerboeck-software-sso"></a>Configurar software ungerboeck SSO

Para configurar um único sinal no lado **do Software Ungerboeck,** é necessário enviar o **valor de impressão digital** e urls copiados apropriados do portal Azure para a equipa de suporte do Software [Ungerboeck](mailto:Rhonda.Jannings@ungerboeck.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Software Ungerboeck.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Ungerboeck Software**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="create-ungerboeck-software-test-user"></a>Criar utilizador de teste de software ungerboeck

Nesta secção, cria-se um utilizador chamado B.Simon in Ungerboeck Software. Trabalhe com a [equipa de suporte do Software Ungerboeck](mailto:Rhonda.Jannings@ungerboeck.com) para adicionar os utilizadores na plataforma de Software Ungerboeck. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

### <a name="test-sso"></a>Teste SSO

Quando selecionar o azulejo do Software Ungerboeck no Painel de Acesso, deverá ser automaticamente inscrito no Software Ungerboeck para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)