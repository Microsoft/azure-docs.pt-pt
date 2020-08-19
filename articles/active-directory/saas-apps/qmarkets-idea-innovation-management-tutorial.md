---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com Qmarkets Idea & Innovation Management / Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Qmarkets Idea & Innovation Management.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/20/2019
ms.author: jeedes
ms.openlocfilehash: 588412691cad2715899888198c33c2630214788f
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88552279"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-qmarkets-idea--innovation-management"></a>Tutorial: Azure Ative Directory integração única (SSO) com Qmarkets Idea & Innovation Management

Neste tutorial, você vai aprender a integrar Qmarkets Idea & Innovation Management com Azure Ative Directory (Azure AD). Quando integra a Qmarkets Idea & Innovation Management com a Azure AD, pode:

* Control em Azure AD que tem acesso à Qmarkets Idea & Innovation Management.
* Capacitar os seus utilizadores a serem automaticamente inscritos na Qmarkets Idea & Innovation Management com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Qmarkets Idea & Innovation Management única subscrição (SSO) habilitada a subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.



* Qmarkets Idea & Innovation Management apoia **SP e IDP** iniciado SSO
* Qmarkets Idea & Innovation Management suporta fornecimento de utilizadores **justos no tempo**


## <a name="adding-qmarkets-idea--innovation-management-from-the-gallery"></a>Adicionar Qmarkets Idea & Innovation Management da galeria

Para configurar a integração da Qmarkets Idea & Innovation Management em Azure AD, é necessário adicionar a Qmarkets Idea & Innovation Management da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add a partir da secção **de galeria,** **digite Qmarkets Idea & Innovation Management** na caixa de pesquisa.
1. Selecione **Qmarkets Idea & Innovation Management** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-qmarkets-idea--innovation-management"></a>Configurar e testar a Azure AD um único sign-on para qmarkets Idea & Innovation Management

Configure e teste Azure AD SSO com Qmarkets Idea & Innovation Management usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na Qmarkets Idea & Innovation Management.

Para configurar e testar o Azure AD SSO com a Qmarkets Idea & Innovation Management, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure a Ideia de Qmarkets & Innovation Management SSO](#configure-qmarkets-idea--innovation-management-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Criar Qmarkets Idea & Utilizador de teste de Gestão](#create-qmarkets-idea--innovation-management-test-user)** de Inovação - ter uma contrapartida da B.Simon in Qmarkets Idea & Innovation Management que está ligada à representação AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Qmarkets Idea & Innovation Management,** encontre a secção **Gerir** e selecione **um único sinal**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<app_url>/sso/saml2/metadata/qmarkets_sp_<endpoint_id>`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<app_url>/sso/saml2/acs/qmarkets_sp_<endpoint_id>`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<app_url>/sso/saml2/endpoint/qmarkets_sp_<endpoint_id>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte [a Qmarkets Idea & equipa de suporte ao cliente de Gestão de Inovação](mailto:support@qmarkets.net) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Qmarkets Idea & Innovation Management.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Qmarkets Idea & Innovation Management**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-qmarkets-idea--innovation-management-sso"></a>Configure Qmarkets Ideia & Gestão da Inovação SSO

Para configurar um único sign-on no **lado da Qmarkets Idea & Innovation Management,** é necessário enviar o **Url de Metadados da Federação de Aplicações** para a [equipa de suporte de gestão de inovação da Qmarkets Idea &](mailto:support@qmarkets.net). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-qmarkets-idea--innovation-management-test-user"></a>Criar utilizadores de teste de & de gestão de inovação da Qmarkets

Nesta secção, um utilizador chamado Britta Simon é criado na Qmarkets Idea & Innovation Management. Qmarkets Idea & Innovation Management suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir na Qmarkets Idea & Innovation Management, uma nova é criada após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Qmarkets Idea & Innovation Management no Painel de Acesso, deverá ser automaticamente inscrito na Qmarkets Idea & Innovation Management para a qual criou o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente a Qmarkets Idea & Innovation Management com a Azure AD](https://aad.portal.azure.com/)

