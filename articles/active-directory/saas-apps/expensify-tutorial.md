---
title: 'Tutorial: Integração do Azure Ative Directory com Expensify Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Expensify.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: jeedes
ms.openlocfilehash: 0be39ebc765efe968f27d948dada1304c0d6d711
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88555251"
---
# <a name="tutorial-integrate-expensify-with-azure-active-directory"></a>Tutorial: Integrar Expensify com Diretório Ativo Azure

Neste tutorial, você vai aprender a integrar Expensify com Azure Ative Diretório (Azure AD). Quando integrar Expensify com Azure AD, pode:

* Controlo em Azure AD que tem acesso a Expensify.
* Permitir que os seus utilizadores sejam automaticamente inscritos para Expensificar com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Expensifique a subscrição ativada por um único sinal (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Expensificar suporta **SP** iniciado SSO

## <a name="adding-expensify-from-the-gallery"></a>Adicionar Expensify da galeria

Para configurar a integração do Expensify no AD AZure, é necessário adicionar Expensify da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite Expensify** na caixa de pesquisa.
1. Selecione **Expensify** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-expensify"></a>Configurar e testar Azure AD único sinal para expensificar

Configure e teste Azure AD SSO com Expensify usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Expensify.

Para configurar e testar o Azure AD SSO com expensificar, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
2. **[Configure Expensify SSO](#configure-expensify-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
    1. **[Crie Expensify utilizador](#create-expensify-test-user)** de teste - para ter uma contraparte de B.Simon em Expensify que está ligada à representação AD Azure do utilizador.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Expensify,** encontre a secção **Gerir** e selecione **'Único sinal de súplica'.**
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL,** digite um URL: `https://www.expensify.com/authentication/saml/login`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL: `https://www.expensify.com`

    c. b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://www.expensify.com/authentication/saml/loginCallback?domain=<yourdomain>`

    > [!NOTE]
    > O valor URL de resposta não é real. Atualize este valor com o URL de resposta real. Contacte a equipa de [apoio ao Cliente](mailto:help@expensify.com) para obter este valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **metadata XML** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configuração Expensify,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Expensify.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Expensify**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-expensify-sso"></a>Configure Expensify SSO

Para ativar o SSO em Expensify, primeiro tem de ativar o **Controlo de Domínio** na aplicação. Pode ativar o Controlo de Domínio na aplicação através dos passos listados [aqui.](https://help.expensify.com/domain-control) Para apoio adicional, trabalhe com [a equipa de apoio ao Cliente Expensify.](mailto:help@expensify.com) Assim que tiver o Controlo de Domínio ativo, siga estes passos:

![Configurar Sign-On Individuais](./media/expensify-tutorial/tutorial_expensify_51.png)

1. Inscreva-se na sua aplicação Expensify.

2. No painel esquerdo, clique em **Definições** e navegue para **SAML**.

3. Alterne a opção **de Login SAML** conforme **Ativado**.

4. Abra os metadados da Federação descarregados a partir do Azure AD no bloco de notas, copie o conteúdo e, em seguida, cole-os na caixa de texto **do Fornecedor de Identidade Metadados.**

### <a name="create-expensify-test-user"></a>Criar Expensify utilizador de teste

Nesta secção, cria-se um utilizador chamado B.Simon in Expensify. Trabalhe com [a equipa de suporte do Cliente Expensify](mailto:help@expensify.com) para adicionar os utilizadores na plataforma Expensify.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Expensify no Painel de Acesso, deverá ser automaticamente inscrito no Expensify para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)