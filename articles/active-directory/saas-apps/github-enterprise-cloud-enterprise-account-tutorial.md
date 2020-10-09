---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com GitHub Enterprise Cloud - Conta Empresarial / Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o GitHub Enterprise Cloud - Conta Empresarial.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/29/2020
ms.author: jeedes
ms.openlocfilehash: 4e23c8a8497459ce7cb3cab3d1469359f80ad846
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88550780"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-github-enterprise-cloud---enterprise-account"></a>Tutorial: Azure Ative Directy integração única (SSO) com GitHub Enterprise Cloud - Conta Empresarial

Neste tutorial, você vai aprender como integrar GitHub Enterprise Cloud - Conta Empresarial com Azure Ative Directory (Azure AD). Quando integrar o GitHub Enterprise Cloud - Conta Empresarial com AZure AD, pode:

* Control em Azure AD que tem acesso a GitHub Enterprise Cloud - Conta Empresarial.
* Habilitado os seus utilizadores a serem automaticamente inscritos na GitHub Enterprise Cloud - Conta Empresarial com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* GitHub Enterprise Cloud - Enterprise Account single sign-on (SSO) ativada subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* GitHub Enterprise Cloud - Conta Empresarial suporta **SP** e **IDP** iniciado SSO
* GitHub Enterprise Cloud - Conta Empresarial suporta provisão de utilizadores **just in time**
* Uma vez configurado GitHub Enterprise Cloud - Enterprise Account pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-github-enterprise-cloud---enterprise-account-from-the-gallery"></a>Adicionar GitHub Enterprise Cloud - Conta Empresarial da galeria

Para configurar a integração da GitHub Enterprise Cloud - Conta Empresarial em Azure AD, é necessário adicionar a GitHub Enterprise Cloud - Conta Empresarial da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **escreva GitHub Enterprise Cloud - Conta Empresarial** na caixa de pesquisa.
1. Selecione **GitHub Enterprise Cloud - Conta Empresarial** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-github-enterprise-cloud---enterprise-account"></a>Configure e teste Azure AD SSO para GitHub Enterprise Cloud - Conta Empresarial

Configure e teste Azure AD SSO com GitHub Enterprise Cloud - Conta Empresarial usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na GitHub Enterprise Cloud - Conta Empresarial.

Para configurar e testar o Azure AD SSO com GitHub Enterprise Cloud - Conta Empresarial, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure a GitHub Enterprise Cloud-Enterprise Account SSO](#configure-github-enterprise-cloud-enterprise-account-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Criar o utilizador de teste de Cloud-Enterprise conta GitHub Enterprise](#create-github-enterprise-cloud-enterprise-account-test-user)** - para ter uma contrapartida de B.Simon em GitHub Enterprise Cloud - Conta Empresarial que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página **gitHub Enterprise Cloud - Enterprise Account** integration, encontre a secção **Gerir** e selecione um único sinal **de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://github.com/enterprises/<ENTERPRISE-SLUG>`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://github.com/enterprises/<ENTERPRISE-SLUG>/saml/consume`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

     Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão:  `https://github.com/enterprises/<ENTERPRISE-SLUG>/sso`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real em URL, URL de resposta e identificador. Contacte [a GitHub Enterprise Cloud - equipa de suporte ao cliente da conta da empresa](mailto:support@github.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificateBase64.png)

1. Na secção **Configurar GitHub Enterprise Cloud - Enterprise Account,** copie os URL(s) apropriados com base na sua exigência.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a GitHub Enterprise Cloud - Conta Empresarial.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **GitHub Enterprise Cloud - Conta Empresarial**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-github-enterprise-cloud-enterprise-account-sso"></a>Configurar GitHub Enterprise Cloud-Enterprise Account SSO

Para configurar um único sign-on no **gitHub Enterprise Cloud -** Enterprise Account side, você precisa enviar o Certificado descarregado **(Base64)** e URLs copiados apropriados do portal Azure para [GitHub Enterprise Cloud - equipa de suporte de conta empresarial](mailto:support@github.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-github-enterprise-cloud-enterprise-account-test-user"></a>Criar o utilizador de teste de conta Cloud-Enterprise empresa GitHub

Nesta secção, um utilizador chamado B.Simon é criado na GitHub Enterprise Cloud - Conta Empresarial. GitHub Enterprise Cloud - Enterprise Account suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no GitHub Enterprise Cloud - Conta Empresarial, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar na Nuvem Empresarial GitHub - Telha de Conta Empresarial no Painel de Acesso, deverá ser automaticamente inscrito na Nuvem Empresarial GitHub - Conta Empresarial para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente GitHub Enterprise Cloud - Conta Empresarial com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger a Nuvem Empresarial GitHub - Conta Empresarial com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)