---
title: 'Tutorial: Azure Ative Directory integração única (SSO) com a Trelica | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Trelica.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: jeedes
ms.openlocfilehash: a674f5f653ad420ab8f28ff73c6b86f9c18b154e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92517757"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trelica"></a>Tutorial: Azure Ative Directory integração única (SSO) com a Trelica

Neste tutorial, aprende-se a integrar a Trelica com o Azure Ative Directory (Azure AD).

Com esta integração, pode:

* Controlo em Azure AD que tem acesso a Trelica.
* Permita que os seus utilizadores sejam automaticamente inscritos na Trelica com as suas contas AD Azure.
* Gerencie as suas contas num local central: o portal Azure.

Para saber mais sobre software como integração de aplicações de serviço (SaaS) com Azure AD, consulte O que é o acesso à [aplicação e um único acesso com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma assinatura Trelica com um único sinal de sso ativado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* A Trelica apoia o SSO iniciado pelo IDP.
* A Trelica suporta o fornecimento de utilizadores just-in-time.
* Depois de configurar a Trelica, pode impor o controlo da sessão. Este controlo protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o acesso condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-trelica-from-the-gallery"></a>Adicionando Trelica da galeria

Para configurar a integração da Trelica em Azure AD, precisa adicionar a Trelica da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta de trabalho ou escola ou uma conta pessoal da Microsoft.
1. No painel de navegação mais à esquerda, selecione o serviço **Azure Ative Directory.**
1. Vá a **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** insira **a Trelica** na caixa de pesquisa.
1. Selecione **Trelica** a partir dos resultados da pesquisa e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-trelica"></a>Configurar e testar Azure AD único sinal para Trelica

Configure e teste Azure AD SSO com Trelica utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, deve estabelecer uma relação ligada entre um utilizador Azure AD e o utilizador relacionado na Trelica.

Para configurar e testar o Azure AD SSO com a Trelica, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user)** para testar o Azure AD com B.Simon.
    1. **[Atribua ao utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que a B.Simon utilize um único sinal de Ad AD.
1. **[Configure o Trelica SSO](#configure-trelica-sso)** para configurar as definições de inscrição única no lado da aplicação.
    1. **[Crie um utilizador de teste Trelica](#create-a-trelica-test-user)** para ter uma contraparte de B.Simon em Trelica. Esta contrapartida está ligada à representação Azure AD do utilizador.
1. **[Teste SSO](#test-sso)** para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir que o Azure AD SSO no portal Azure:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **da Trelica,** aceda à secção **Gerir.** Selecione **um único sinal de inscrição**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set up Single Sign-on com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![O set up Single Sign-On com página SAML, com o ícone de lápis para configuração básica SAML em destaque](common/edit-urls.png)

1. Na **configuração de 'Sind on' único com** a página SAML, insira os seguintes valores:

    1. Na caixa **identifier,** introduza o URL **https://app.trelica.com** .

    1. Na caixa **URL de resposta,** introduza um URL com o padrão `https://app.trelica.com/Id/Saml2/<CUSTOM_IDENTIFIER>/Acs` .

    > [!NOTE]
    > O valor URL de resposta não é real. Atualize este valor com o URL de resposta real (também conhecido como ACS).
    > Pode encontrá-lo iniciando sessão na Trelica e indo para a página de [configuração de fornecedores de identidade SAML](https://app.trelica.com/Admin/Profile/SAML) (Conta > Admin > SAML). Clique no botão de cópia ao lado do URL do **Serviço de Consumo de Afirmação (ACS)** para o colocar na área de transferência, pronto para colar na caixa de texto URL **answer** em Azure AD.
    > Leia a [documentação da Ajuda da Trelica](https://docs.trelica.com/admin/saml/azure-ad) ou contacte a equipa de suporte do [Cliente Trelica](mailto:support@trelica.com) se tiver dúvidas.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** clique no botão de cópia para copiar o Url de **metadados da Federação de Aplicações** e guarde-o no seu computador.

    ![A secção de Certificado de Assinatura SAML, com o botão de cópia realçado ao lado do Url de Metadados da Federação de Aplicações](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, cria-se um utilizador de teste chamado B.Simon no portal Azure.

1. No painel mais à esquerda do portal Azure, selecione **Utilizadores do Diretório Ativo Azure**  >    >  **Todos os utilizadores**.
1. Na parte superior do ecrã, selecione **Novo utilizador**.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome,** insira **B.Simon**.
   1. No campo **nome do utilizador,** introduza **B.Simon@**_de empresa_**.** _extensão_. Por exemplo, B.Simon@contoso.com.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor mostrado na caixa **palavra-passe.**
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que B.Simon use Azure single sign-on, concedendo acesso à Trelica.

1. No portal Azure, selecione **Aplicações empresariais**  >  **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Trelica.**
1. Na página geral da aplicação, vá à secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A secção Gerir, com Utilizadores e grupos em destaque](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**. Na caixa de diálogo **de atribuição de** adicionar, selecione **Utilizadores e grupos**.

   ![A janela dos Utilizadores e grupos, com o Utilizador Adicionar em destaque](common/add-assign-user.png)

1. Na caixa de diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de utilizadores. Em seguida, escolha o botão **Selecione** na parte inferior do ecrã.
1. Se espera algum valor de função na afirmação SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista. Em seguida, escolha o botão **Selecione** na parte inferior do ecrã.
1. Na caixa de diálogo **'Adicionar Atribuição',** selecione **Atribuir**.

## <a name="configure-trelica-sso"></a>Configurar Trelica Sso

Para configurar um único sinal no lado **trelica,** aceda à página de [configuração dos fornecedores de identidade SAML](https://app.trelica.com/Admin/Profile/SAML) (Conta > Admin > SAML). Clique no botão **Novo.** Introduza **o Azure AD** como nome e escolha **metadados do url** para o tipo de metadados. Cole o **url de metadados da Federação de Aplicações** que tirou do Azure AD para o campo **url de metadados** em Trelica.

Leia a [documentação da Ajuda da Trelica](https://docs.trelica.com/admin/saml/azure-ad) ou contacte a equipa de suporte do [Cliente Trelica](mailto:support@trelica.com) se tiver dúvidas.

### <a name="create-a-trelica-test-user"></a>Criar um utilizador de teste Trelica

A Trelica suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há ação para tomares nesta secção. Se um utilizador já não existir na Trelica, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de Azure AD utilizando o portal My Apps.

Quando seleciona o azulejo Trelica no portal My Apps, está automaticamente inscrito na Trelica para a qual configura sSO. Para obter mais informações sobre o portal My Apps, consulte [Introdução ao portal My Apps](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais para integrar apps SaaS com Diretório Ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente Trelica com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger a Trelica com visibilidade e controlos avançados](/cloud-app-security/proxy-intro-aad)