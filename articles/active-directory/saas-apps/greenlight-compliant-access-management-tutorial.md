---
title: 'Tutorial: Azure Ative Directy integração única de sign-on (SSO) com Greenlight Compliant Access Management / Microsoft Docs'
description: Saiba como configurar um único sinal de acesso entre o Azure Ative Directory e a Greenlight Compliant Access Management.
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
ms.openlocfilehash: 291d9de822ebb4d4f134be3788b84a3f8aded301
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88551494"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-greenlight-compliant-access-management"></a>Tutorial: Azure Ative Directory integração única de sign-on (SSO) com Greenlight Compliant Access Management

Neste tutorial, você vai aprender a integrar Greenlight Compliant Access Management com Azure Ative Directory (Azure AD). Quando integrar a Greenlight Compliant Access Management com a Azure AD, pode:

* Controlo em Azure AD que tem acesso à Greenlight Compliant Access Management.
* Capacitar os seus utilizadores a serem automaticamente inscritos na Greenlight Compliant Access Management com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Gestão de acesso compatível com luz verde uma única assinatura ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Greenlight Compliant Access Management suporta **SP e IDP** iniciado SSO
* Assim que configurar a Greenlight Compliant Access Management, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-greenlight-compliant-access-management-from-the-gallery"></a>Adicionar Greenlight Gestão de Acesso compatível da galeria

Para configurar a integração da Greenlight Compliant Access Management em AD, precisa adicionar a Greenlight Compliant Access Management da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite Greenlight Compliant Access Management** na caixa de pesquisa.
1. Selecione **Greenlight Compliant Access Management** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-greenlight-compliant-access-management"></a>Configurar e testar a Azure AD um único sinal de acesso para a Greenlight Compliant Access Management

Configure e teste Azure AD SSO com Greenlight Compliant Access Management usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na Greenlight Compliant Access Management.

Para configurar e testar o Azure AD SSO com a Greenlight Compliant Access Management, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o SSO de Gestão de Acesso compatível com luz verde](#configure-greenlight-compliant-access-management-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create Greenlight Compliant Access Management test user](#create-greenlight-compliant-access-management-test-user)** - para ter uma contraparte de B.Simon in Greenlight Compliant Access Management que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Greenlight Compliant Access Management,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<CUSTOMER>.greenlightcorp.com/ebcpresq/checkLoginSAML.do`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<CUSTOMER>.greenlightcorp.com/ebcpresq/checkLoginSAML.do`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<CUSTOMER>.greenlightcorp.com/ebcpresq/checkLoginSAML.do`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte [a equipa de suporte ao cliente de gestão de acessos compatíveis com](mailto:support@greenlightcorp.com) a Greenlight para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção Configurar a **Gestão de Acesso compatível com luz verde,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Greenlight Compliant Access Management.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Greenlight Compliant Access Management**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-greenlight-compliant-access-management-sso"></a>Configurar a Gestão de Acessos Compatíveis com luz verde SSO

Para configurar um único sinal de acesso no lado **da Greenlight Compliant Access Management,** é necessário enviar os **Metadados XML da Federação** descarregados e URLs copiados apropriados do portal Azure para a equipa de suporte de [gestão de acesso compatíveis com luz verde.](mailto:support@greenlightcorp.com) Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-greenlight-compliant-access-management-test-user"></a>Criar utilizador de teste de gestão de acesso compatível com luz verde

Nesta secção, cria-se um utilizador chamado B.Simon in Greenlight Compliant Access Management. Trabalhe com a [equipa de suporte da Greenlight Compliant Access Management](mailto:support@greenlightcorp.com) para adicionar os utilizadores na plataforma Greenlight Compliant Access Management. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo de Gestão de Acesso compatível com luz verde no Painel de Acesso, deverá ser automaticamente inscrito na Gestão de Acesso compatível com luz verde para a qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente a Greenlight Compliant Access Management com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger a Gestão de Acesso compatível com Luz Verde com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)