---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com Plataforma de Integração De Luz Verde Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Plataforma de Integração Greenlight.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/03/2020
ms.author: jeedes
ms.openlocfilehash: 4323968584c11489a7a748a6eacc65c511ddf23b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88551455"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-greenlight-integration-platform"></a>Tutorial: Azure Ative Directory integração única (SSO) com plataforma de integração de luz verde

Neste tutorial, você vai aprender a integrar a Plataforma de Integração Greenlight com o Azure Ative Directory (Azure AD). Quando integrar a Plataforma de Integração Greenlight com a AD Azure, pode:

* Control em Azure AD que tem acesso à Plataforma de Integração Greenlight.
* Capacitar os seus utilizadores a serem automaticamente inscritos na Plataforma de Integração Greenlight com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura ativada pela Plataforma de Integração greenlight (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Plataforma de Integração greenlight suporta SSO iniciado **sp e IDP**
* Uma vez configurada Plataforma de Integração De Luz Verde, pode impor o controlo de sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-greenlight-integration-platform-from-the-gallery"></a>Adicionar plataforma de integração greenlight da galeria

Para configurar a integração da Plataforma de Integração Greenlight no AD Azure, é necessário adicionar a Plataforma de Integração Greenlight da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** digite **Plataforma de Integração Greenlight** na caixa de pesquisa.
1. Selecione a Plataforma de **Integração Greenlight** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-greenlight-integration-platform"></a>Configurar e testar a Azure AD um único sinal para a Plataforma de Integração greenlight

Configure e teste Azure AD SSO com a Greenlight Integration Platform usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na Plataforma de Integração Greenlight.

Para configurar e testar o Azure AD SSO com a Plataforma de Integração Greenlight, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure a Plataforma de Integração Greenlight SSO](#configure-greenlight-integration-platform-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create Greenlight Integration Platform test user](#create-greenlight-integration-platform-test-user)** - para ter uma contrapartida de B.Simon na Plataforma de Integração Greenlight que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **da Plataforma de Integração Greenlight,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<CUSTOMER>.greenlightcorp.com/ebcprtads/checkLoginSAML.do`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<CUSTOMER>.greenlightcorp.com/ebcprtads/checkLoginSAML.do`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<CUSTOMER>.greenlightcorp.com/ebcprtads/checkLoginSAML.do`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte a [equipa de suporte do Cliente da Plataforma de Integração Greenlight](mailto:support@greenlightcorp.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Plataforma de Integração De Luz Verde,** copie os URL(s) apropriados com base na sua exigência.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Plataforma de Integração Greenlight.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Greenlight Integration Platform**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-greenlight-integration-platform-sso"></a>Configure a plataforma de integração de luz verde SSO

Para configurar um único sinal no lado da Plataforma de **Integração Greenlight,** é necessário enviar os **Metadados XML da Federação** descarregados e URLs copiados apropriados do portal Azure para a equipa de suporte da Plataforma de [Integração Greenlight.](mailto:support@greenlightcorp.com) Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-greenlight-integration-platform-test-user"></a>Criar utilizador de teste de plataforma de integração de luz verde

Nesta secção, cria-se um utilizador chamado B.Simon in Greenlight Integration Platform. Trabalhe com a [equipa de suporte da Plataforma de Integração Greenlight](mailto:support@greenlightcorp.com) para adicionar os utilizadores na plataforma da Plataforma de Integração Greenlight. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo da Plataforma de Integração Greenlight no Painel de Acesso, deverá ser automaticamente inscrito na Plataforma de Integração Greenlight para a qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente a Plataforma de Integração Greenlight com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger a Plataforma de Integração Greenlight com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)