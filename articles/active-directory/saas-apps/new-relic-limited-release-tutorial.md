---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com Nova Relíquia (Por Organização) / Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o New Relic (By Organization).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: jeedes
ms.openlocfilehash: 62732f92d0739018863229da0a05646cfa817006
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2020
ms.locfileid: "91941036"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic-by-organization"></a>Tutorial: Azure Ative Directory integração única (SSO) com Nova Relíquia (Por Organização)

Neste tutorial, você vai aprender a integrar New Relic (By Organization) com Azure Ative Directory (Azure AD). Quando integras a Nova Relíquia (Por Organização) com a Azure AD, podes:

* Controle em Azure AD que tem acesso a Nova Relíquia (Por Organização).
* Permita que os seus utilizadores sejam automaticamente inscritos na New Relic (By Organization) com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Nova assinatura ativada por Relíquia (Por Organização) (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Nova Relíquia (Por Organização) apoia **IDP** iniciado SSO

* Uma vez configurada a Nova Relíquia, pode impor controlos de sessão, que protegem a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controlos de sessão estendem-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-new-relic-by-organization-from-the-gallery"></a>Adicionar Nova Relíquia (Por Organização) da galeria

Para configurar a integração de New Relic (By Organization) no AD Azure, é necessário adicionar New Relic (By Organization) da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** escreva **Nova Relíquia (Por Organização)** na caixa de pesquisa.
1. Selecione **New Relic (Por Organização)** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-new-relic-by-organization"></a>Configure e teste Azure AD único sign-on para New Relic (By Organization)

Configure e teste Azure AD SSO com Nova Relíquia (Por Organização) usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em New Relic (By Organization).

Para configurar e testar a Azure AD SSO com Nova Relíquia (Por Organização), complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure a Nova Relíquia (Por Organização) SSO](#configure-new-relic-by-organization-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create New Relic (By Organization) test user](#create-new-relic-by-organization-test-user)** - para ter uma contraparte de B.Simon em New Relic (By Organization) que está ligada à representação AZure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **New Relic (By Organization),** encontre a secção **Gerir** e selecione **um único sinal**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** se tiver **um ficheiro de metadados do Fornecedor de Serviços,** execute os seguintes passos:

    a. Clique **em Carregar o ficheiro de metadados**.

    ![A screenshot mostra a configuração básica do SAML com o link de ficheiro de metadados upload.](common/upload-metadata.png)

    b. Clique no **logotipo da pasta** para selecionar o ficheiro de metadados e clique em **Upload**.

    ![A screenshot mostra uma caixa de diálogo onde pode selecionar e carregar um ficheiro.](common/browse-upload-metadata.png)

    c. Uma vez que o ficheiro de metadados é carregado com sucesso, os valores de URL **de identificação** e **resposta** são preenchidos automaticamente na secção de Configuração BÁSICA SAML:

    ![A screenshot mostra a Configuração BÁSICA SAML, onde pode introduzir o Identificador, Responder U R L e selecionar Guardar.](common/idp-intiated.png)

    > [!Note]
    > Se os valores de URL **de identificação** e **resposta** não estiverem a ser automaticamente poluídos, preencha os valores manualmente de acordo com o seu requisito.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** clique no botão de cópia para copiar o Url de **metadados da Federação de Aplicações** e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/copy-metadataurl.png)

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a New Relic (By Organization).

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Nova Relíquia (Por Organização)**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-new-relic-by-organization-sso"></a>Configure nova relíquia (por organização) SSO

Para configurar um único sign-on no lado **de New Relic (By Organization),** você precisa enviar o url de **metadados da Federação de Aplicações** para a equipa de [suporte New Relic (By Organization).](https://support.newrelic.com/) Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.


### <a name="create-new-relic-by-organization-test-user"></a>Criar novo utilizador de teste de relíquias (por organização)

Nesta secção, cria-se um utilizador chamado B.Simon in New Relic (By Organization). Trabalhe com a [equipa de apoio da New Relic (By Organization)](https://support.newrelic.com/) para adicionar os utilizadores na plataforma New Relic (By Organization). Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo New Relic (By Organization) no Painel de Acesso, deverá ser automaticamente inscrito na Nova Relíquia (Por Organização) para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente nova relíquia (por organização) com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)