---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com Lenses.io Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Lenses.io.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/02/2020
ms.author: jeedes
ms.openlocfilehash: 0b8d736ab169ad07bd23a21d3a420bb6a044bf01
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88528646"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-the-lensesio-dataops-portal"></a>Tutorial: Azure Ative Directory integração única de sign-on (SSO) com o portal Lenses.io DataOps.

Neste tutorial, você vai aprender a integrar o [portal dataOps Lenses.io](https://lenses.io/) com Azure Ative Directory (Azure AD). Quando integra Lenses.io com a AD Azure, pode:

* Controlo em Azure AD que tem acesso ao portal Lenses.io.
* Ative os seus utilizadores a serem automaticamente inscritos nas Lentes com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Um exemplo de um portal lenses. Pode implantar um portal Lenses de [várias maneiras.](https://lenses.io/product/deployment/)
* Uma [licença](https://lenses.io/product/pricing/) Lenses.io que suporta uma única assinatura (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Lenses.io apoia SSO iniciado **pela SP**

* Uma vez configurados Lenses.io pode impor o controlo da sessão, que protegem a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-lensesio-from-the-gallery"></a>Adicionar Lenses.io da galeria

Para configurar a integração de Lenses.io no AD Azure, é necessário adicionar Lenses.io da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** escreva **Lenses.io** na caixa de pesquisa.
1. Selecione **Lenses.io** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-lensesio"></a>Configure e teste Azure AD SSO para Lenses.io

Configure e teste Azure AD SSO com o seu portal Lenses.io usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Lenses.io.

Para configurar e testar o Azure AD SSO com Lenses.io, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador e grupo de teste AD Azure](#create-an-azure-ad-test-user-and-group)** - para testar o Azure AD com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure Lenses.io SSO](#configure-lensesio-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Crie permissões de grupo de teste Lenses.io](#create-lensesio-test-group-permissions)** - para controlar o que b.Simon deve aceder em Lenses.io (autorização).
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Lenses.io,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<CUSTOMER_LENSES_BASE_URL>` por exemplo. `https://lenses.my.company.com`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<CUSTOMER_LENSES_BASE_URL>` por exemplo. `https://lenses.my.company.com`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<CUSTOMER_LENSES_BASE_URL>/api/v2/auth/saml/callback?client_name=SAML2Client`
    por exemplo. `https://lenses.my.company.com/api/v2/auth/saml/callback?client_name=SAML2Client`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real em URL, URL de resposta e identificador, com base no URL base da sua instância do portal Lenses. Pode encontrar mais informações na [documentação Lenses.io SSO.](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0)

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configuração Lenses.io,** utilize o ficheiro XML acima para configurar lentes contra o seu Azure SSO.

### <a name="create-an-azure-ad-test-user-and-group"></a>Criar um utilizador e grupo de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon. Também irá criar um grupo de teste para B.Simon que será usado para controlar o acesso que B.Simon tem em Lenses.
Você pode descobrir como lenses usa mapeamento de membro do grupo para autorização na [documentação Lenses SSO](https://docs.lenses.io/install_setup/configuration/security.html#id3)

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

Para criar o grupo:
1. Volte para **o Azure Ative Directory**, selecione **Groups**
1. Selecione **Novo grupo** na parte superior do ecrã.
1. Nas propriedades do **Grupo,** siga estes passos:
   1. No campo **do tipo Grupo,** selecione `Security` .
   1. No campo **Nome de Grupo,** insira `LensesUsers`
   1. Clique em **Criar**.
1. Selecione o grupo `LensesUsers` e tome nota do **Id do Objeto** (por `f8b5c1ec-45de-4abd-af5c-e874091fb5f7` exemplo). Este ID será usado em Lentes para mapear os utilizadores desse grupo para as [permissões corretas.](https://docs.lenses.io/install_setup/configuration/security.html#id3)  
   
Para atribuir o grupo ao utilizador de teste: 
1. Volte para o **Azure Ative Directory**, selecione **Utilizadores**.
1. Selecione o utilizador de teste `B.Simon` .
1. Selecione **Grupos**.
1. **Selecione Adicionar os membros** na parte superior do ecrã.
1. Procure `LensesUsers` e selecione.
1. Clique em **Selecionar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, permitindo o acesso a Lenses.io.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Lenses.io**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-lensesio-sso"></a>Configurar Lenses.io SSO

Para configurar um único sinal no portal **Lenses.io,** instale o **Metdata XML da Federação** descarregado na sua instância lenses e [configura lentes para permitir o SSO](https://docs.lenses.io/install_setup/configuration/security.html#configure-lenses). 

### <a name="create-lensesio-test-group-permissions"></a>Criar permissões de grupo de teste Lenses.io

Nesta secção, cria-se um grupo em Lentes utilizando o **ID de Objeto** do `LensesUsers` grupo que observamos na secção de criação de [utilizadores.](#create-an-azure-ad-test-user-and-group)
Atribui as permissões desejadas que `B.Simon` devem ter em Lenses.
Pode encontrar mais informações sobre o mapeamento do [grupo Azure - Lenses.](https://docs.lenses.io/install_setup/configuration/security.html#azure-groups)

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Lenses.io no Painel de Acesso, deverá ser automaticamente inscrito no portal Lenses.io para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [ Configurar sSO na sua Lenses.io instância ](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0)

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente Lenses.io com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger Lenses.io com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
