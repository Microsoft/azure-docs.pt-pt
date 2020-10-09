---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com Lenses.io Microsoft Docs'
description: Neste tutorial, você aprenderá a configurar um único sign-on entre Azure Ative Directory e Lenses.io.
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
ms.openlocfilehash: 48a1e50d451abb429e9bc33308909b368283644f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88661457"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-the-lensesio-dataops-portal"></a>Tutorial: Azure Ative Directory integração única (SSO) com o portal Lenses.io DataOps

Neste tutorial, você vai aprender a integrar o [portal dataOps Lenses.io](https://lenses.io/) com Azure Ative Directory (Azure AD). Depois de integrar Lenses.io com a Azure AD, pode:

* Controlo em Azure AD que tem acesso ao portal Lenses.io.
* Ative os seus utilizadores a serem automaticamente inscritos nas Lentes com as suas contas AD Azure.
* Gerencie as suas contas num local central: o portal Azure.

Para saber mais sobre software como integração de aplicações de serviço (SaaS) com Azure AD, consulte O que é o acesso à [aplicação e um único acesso com Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Um exemplo de um portal lenses. Pode escolher entre várias opções de [implementação.](https://lenses.io/product/deployment/)
* Uma [licença](https://lenses.io/product/pricing/) Lenses.io que suporta uma única assinatura (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você vai configurar e testar Azure AD SSO em um ambiente de teste.

* Lenses.io suporta o prestador de serviços (SP) iniciado sSO.

* Pode impor o controlo da sessão depois de configurar Lenses.io. O controlo da sessão protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-lensesio-from-the-gallery"></a>Adicione Lenses.io da galeria

Para configurar a integração de Lenses.io no AD Azure, adicione Lenses.io à sua lista de aplicações geridas para o SaaS:

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta de trabalho ou escola ou uma conta pessoal da Microsoft.
1. No painel esquerdo, selecione o serviço **Azure Ative Directory.**
1. Vá a **Aplicações Empresariais**e, em seguida, selecione **Todas as Aplicações**.
1. Selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** introduza **Lenses.io** na caixa de pesquisa.
1. A partir do painel de resultados, selecione **Lenses.io**e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-lensesio"></a>Configure e teste Azure AD SSO para Lenses.io

Irá criar um utilizador de teste chamado *B.Simon* para configurar e testar o Azure AD SSO com o seu portal Lenses.io. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Lenses.io.

Siga os seguintes passos:

1. [Configure Azure AD SSO](#configure-azure-ad-sso) para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. [Crie um utilizador e grupo de teste AZure AD](#create-an-azure-ad-test-user-and-group) para testar O Azure AD SSO com B.Simon.
    1. [Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user) para permitir que a B.Simon utilize o Azure AD SSO.
1. [Configurar Lenses.io SSO](#configure-lensesio-sso) para configurar as definições SSO no lado da aplicação.
    1. [Crie permissões de grupo de teste Lenses.io](#create-lensesio-test-group-permissions) para controlar o que a B.Simon pode aceder em Lenses.io (autorização).
1. [Teste SSO](#test-sso) para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir que o Azure AD SSO no portal Azure:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Lenses.io,** encontre a secção **Gerir** e, em seguida, selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, selecione o ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Screenshot que mostra o ícone para editar a configuração básica do SAML.](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** introduza valores nas seguintes caixas de entrada de texto:

    a. **Assine no URL**: Introduza um URL com o seguinte padrão: `https://<CUSTOMER_LENSES_BASE_URL>` . Um exemplo é `https://lenses.my.company.com`.

    b. **Identificador (ID da entidade)**: Introduza um URL com o seguinte padrão: `https://<CUSTOMER_LENSES_BASE_URL>` . Um exemplo é `https://lenses.my.company.com`.

    c. **URL de resposta**: Introduza um URL com o seguinte padrão: `https://<CUSTOMER_LENSES_BASE_URL>/api/v2/auth/saml/callback?client_name=SAML2Client` . Um exemplo é `https://lenses.my.company.com/api/v2/auth/saml/callback?client_name=SAML2Client`.

    > [!NOTE]
    > Estes valores não são reais. Atualize-os com o URL de inscrição real, URL de resposta e identificador do URL base da sua instância do portal Lenses. Consulte a [documentação Lenses.io SSO](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0) para obter mais informações.

1. Na **configuração de um único sessão de inscrição com** a página SAML, aceda à secção **de Certificado de Assinatura SAML.** Encontre **o Metadados XML da Federação**e, em seguida, selecione **Baixar** para descarregar e guardar o certificado no seu computador.

    ![Screenshot que mostra o link de descarregamento do Certificado.](common/metadataxml.png)

1. Na secção **Configuração Lenses.io,** utilize o ficheiro XML que descarregou para configurar lentes contra o seu Azure SSO.

### <a name="create-an-azure-ad-test-user-and-group"></a>Criar um utilizador e grupo de teste AZure AD

No portal Azure, vai criar um utilizador de teste chamado B.Simon. Depois vais criar um grupo de teste que controla o acesso que o B.Simon tem em Lentes.

Pode descobrir como o Lenses utiliza mapeamento de membros do grupo para autorização na [documentação Lenses SSO](https://docs.lenses.io/install_setup/configuration/security.html#id3).

**Para criar o utilizador de teste:**

1. No painel esquerdo do portal Azure, selecione **O Diretório Ativo Azure,** selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Na parte superior do ecrã, selecione **Novo utilizador**.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. Na caixa **Nome,** insira **B.Simon**.  
   1. Na caixa **de nome do utilizador,** introduza o username@companydomain.extension . Por exemplo, B.Simon@contoso.com.
   1. Selecione a caixa **de verificação de senha show.** Escreva a palavra-passe que mostra na caixa **de senha.**
   1. Selecione **Criar**.

**Para criar o grupo:**

1. Vá ao **Azure Ative Directory**e, em seguida, selecione **Grupos**.
1. No topo do ecrã, selecione **Novo grupo**.
1. Nas propriedades do **Grupo,** siga estes passos:
   1. Na caixa **de tipo grupo,** selecione **Security**.
   1. Na caixa **Nome de Grupo,** **insira LensesUsers**.
   1. Selecione **Criar**.
1. Selecione o grupo **LensesUsers** e copie o **Object ID** (por exemplo, f8b5c1ec-45de-4abd-af5c-e874091fb5f7). Você usará este ID em Lentes para mapear os utilizadores do grupo para as [permissões corretas.](https://docs.lenses.io/install_setup/configuration/security.html#id3)  

**Para atribuir o grupo ao utilizador de teste:**

1. Vá ao **Azure Ative Directory**e, em seguida, selecione **Utilizadores**.
1. Selecione o utilizador de teste **B.Simon**.
1. Selecione **Grupos**.
1. Na parte superior do ecrã, **selecione Adicionar membros**.
1. Procure e **selecione LensesUsers**.
1. Clique em **Selecionar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, permitindo o acesso a Lenses.io.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Lenses.io**.
1. Na página geral da aplicação, na secção **Gerir,** selecione **Utilizadores e grupos**.

   ![Screenshot que mostra a ligação "Utilizadores e grupos".](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**.

   ![Screenshot que mostra o link do utilizador adicionar.](common/add-assign-user.png)

1. Na caixa de diálogo **de atribuição de** adicionar, selecione **Utilizadores e grupos**.
1. Na caixa de diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores. Em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, na caixa de diálogo **Select Role,** escolha a função adequada para o utilizador da lista. Em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. Na caixa de diálogo **'Adicionar Atribuição',** selecione o botão **'Atribuir'.**

## <a name="configure-lensesio-sso"></a>Configurar Lenses.io SSO

Para configurar o SSO no portal **Lenses.io,** instale o **Metdata XML da Federação** descarregado na sua instância Lenses e [configure lentes para permitir o SSO](https://docs.lenses.io/install_setup/configuration/security.html#configure-lenses).

### <a name="create-lensesio-test-group-permissions"></a>Criar permissões de grupo de teste Lenses.io

1. Para criar um grupo em Lentes, utilize o **ID** do Objeto do grupo **LensesUsers.** Este é o ID que copiou na secção de criação de [utilizadores.](#create-an-azure-ad-test-user-and-group)
1. Atribua as permissões desejadas para B.Simon.

Para mais informações, consulte [o Mapeamento do grupo Azure - Lenses.](https://docs.lenses.io/install_setup/configuration/security.html#azure-groups)

## <a name="test-sso"></a>Teste SSO

Nesta secção, teste a sua configuração Azure AD SSO utilizando o Painel de Acesso.

Quando selecionar o azulejo Lenses.io no Painel de Acesso, deverá ser automaticamente inscrito no seu portal Lenses.io. Para mais informações, consulte [Introduction to the Access Panel (Introdução ao Painel de Acesso)](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Configurar sSO na sua Lenses.io instância](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0)

- [Lista de tutoriais sobre como integrar apps saaS com Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e sSO com Azure AD?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure AD?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente Lenses.io com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger Lenses.io com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
