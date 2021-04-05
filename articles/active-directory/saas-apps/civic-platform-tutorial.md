---
title: 'Tutorial: Integração do Diretório Ativo Azure com plataforma cívica | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Plataforma Cívica.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/25/2019
ms.author: jeedes
ms.openlocfilehash: 26109d4b8875dd0b442521513dbd219dc0de06e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92455974"
---
# <a name="tutorial-integrate-civic-platform-with-azure-active-directory"></a>Tutorial: Integrar plataforma cívica com diretório ativo Azure

Neste tutorial, você vai aprender a integrar a Plataforma Cívica com o Azure Ative Directory (Azure AD). Quando integrar a Plataforma Cívica com AZure AD, pode:

* Controlo em Azure AD que tem acesso à Plataforma Cívica.
* Permita que os seus utilizadores sejam automaticamente inscritos na Plataforma Cívica com as suas contas AD AZure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode ter um mês de teste gratuito [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura ativada pela Plataforma Cívica (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Plataforma Cívica apoia SSO iniciado **pela SP**





## <a name="adding-civic-platform-from-the-gallery"></a>Adicionando plataforma cívica da galeria

Para configurar a integração da Plataforma Cívica no AD Azure, é necessário adicionar a Plataforma Cívica da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** escreva **plataforma cívica** na caixa de pesquisa.
1. Selecione **Plataforma Cívica** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Configure e teste Azure AD SSO com Plataforma Cívica usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na Plataforma Cívica.

Para configurar e testar o Azure AD SSO com plataforma cívica, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure a Plataforma Cívica SSO](#configure-civic-platform-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
5. **[Create Civic Platform test user](#create-civic-platform-test-user)** - ter uma contrapartida de B.Simon na Plataforma Cívica que está ligada à representação AD AD do utilizador.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações da **Plataforma Cívica,** encontre a secção **Gerir** e selecione **'Único sinal de súb'.**
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.accela.com`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL: `civicplatform.accela.com`

    > [!NOTE]
    > O valor do sinal de URL não é real. Atualize este valor com o sinal real no URL. Contacte [a equipa de apoio ao Cliente da Plataforma Cívica](mailto:skale@accela.com) para obter este valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** clique no botão de cópia para copiar o **Url de Metadados da Federação de Aplicações** e guarde-o no seu computador.

    ![O Screenshot mostra a página do Certificado de Assinatura SAML onde pode copiar metadados da Federação de Aplicações que é l.](common/copy-metadataurl.png)

1. Navegue para as inscrições da **Azure Ative Directory**  >  **App** em Azure AD, selecione a sua aplicação.

1. Copie o **ID do Diretório (inquilino)** e guarde-o no Bloco de Notas.

    ![Copie o diretório (ID do inquilino) e guarde-o no seu código de aplicações](media/civic-platform-tutorial/directoryid.png)

1. Copie o **ID da aplicação** e guarde-o no Bloco de Notas.

   ![Copiar o ID da aplicação (cliente)](media/civic-platform-tutorial/applicationid.png)

1. Navegue para as inscrições da **Azure Ative Directory**  >  **App** em Azure AD, selecione a sua aplicação. Selecione **Certificados & segredos**.

1. Selecione **segredos do Cliente - > novo segredo do cliente**.

1. Forneça uma descrição do segredo, e uma duração. Quando terminar, **selecione Adicionar**.

   > [!NOTE]
   > Depois de salvar o segredo do cliente, o valor do segredo do cliente é exibido. Copie este valor porque não é capaz de recuperar a chave mais tarde.

   ![Copie o valor secreto porque não pode recuperar isto mais tarde](media/civic-platform-tutorial/secretkey.png)

### <a name="configure-civic-platform-sso"></a>Configurar plataforma cívica SSO

1. Abra uma nova janela do navegador web e inscreva-se no site da empresa Atlassian Cloud como administrador.

1. Clique em **Escolhas Padrão**.

    ![O screenshot mostra o site Atlassian Cloud com escolhas padrão chamadas em Ferramentas de Administrador.](media/civic-platform-tutorial/standard-choices.png)

1. Crie um **ssoconfig de** escolha padrão.

1. Procure **por ssoconfig**  e submeta-se.

    ![Screenshot mostra Standard Choices Search com o nome s o config introduzido.](media/civic-platform-tutorial/sso-config.png)

1. Expanda o SSOCONFIG clicando no ponto vermelho.

    ![Screenshot mostra Escolhas Padrão Navegar com S O CONFIG disponível.](media/civic-platform-tutorial/sso-config01.png)

1. Fornecer informações de configuração relacionadas com SSO no passo seguinte:

    ![A screenshot mostra o edição de item escolhas padrão para S O CONFIG.](media/civic-platform-tutorial/sso-config02.png)

    1. No campo **applicationid,** insira o valor **de ID de aplicação,** que copiou a partir do portal Azure.

    1. No campo **clienteSret,** insira o valor **Secret,** que copiou a partir do portal Azure.

    1. No campo **Diretório,** insira o valor de ID do **Diretório (inquilino),** que copiou do portal Azure.

    1. Insira o idpName. Ex:- `Azure` .

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Plataforma Cívica.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Plataforma Cívica.**
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="create-civic-platform-test-user"></a>Criar utilizador de teste da Plataforma Cívica

Nesta secção, cria-se um utilizador chamado B.Simon na Plataforma Cívica. Trabalhe com a equipa de apoio da Plataforma Cívica para adicionar os utilizadores na [equipa de apoio ao Cliente da Plataforma Cívica.](mailto:skale@accela.com) Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

### <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Ao clicar no azulejo da Plataforma Cívica no Painel de Acesso, deverá ser automaticamente inscrito na Plataforma Cívica para a qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)