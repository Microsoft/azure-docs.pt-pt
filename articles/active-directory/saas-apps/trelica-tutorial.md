---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com trelica [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Trelica.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 577bdae2-dbab-4445-a07e-de0119b65d76
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17c68a95530f345d1ec0ed077681ec4cd6eb3775
ms.sourcegitcommit: 90d2d95f2ae972046b1cb13d9956d6668756a02e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/14/2020
ms.locfileid: "83402445"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trelica"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com trelica

Neste tutorial, aprende-se a integrar a Trelica com o Azure Ative Directory (Azure AD).

Com esta integração, pode:

* Controlo em Azure AD que tem acesso a Trelica.
* Permita que os seus utilizadores sejam automaticamente inscritos na Trelica com as suas contas Azure AD.
* Gerencie as suas contas num local central: o portal Azure.

Para saber mais sobre software como uma integração de aplicações de serviço (SaaS) com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma assinatura Trelica com um único sinal (SSO) ativado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* A Trelica suporta o SSO iniciado pelo IDP.
* A Trelica suporta o fornecimento de utilizadores just-in-time.
* Depois de configurar a Trelica, pode impor o controlo da sessão. Este controlo protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o acesso condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

## <a name="adding-trelica-from-the-gallery"></a>Adicionando Trelica da galeria

Para configurar a integração da Trelica em Azure AD, precisa adicionar Trelica da galeria à sua lista de aplicações geridas do SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta de trabalho ou de escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação mais à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Vá a **Aplicações Empresariais**e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **da galeria,** introduza **trelica** na caixa de pesquisa.
1. Selecione **Trelica** a partir dos resultados da pesquisa e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-trelica"></a>Configure e teste Azure AD único sign-on para Trelica

Configure e teste Azure AD SSO com Trelica utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, deve estabelecer uma relação ligada entre um utilizador da AD Azure e o utilizador relacionado em Trelica.

Para configurar e testar o Azure AD SSO com trelica, complete os seguintes blocos de construção:

1. **[Configure O SSO AD Azure](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** para testar o único sinal de Azure AD com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure o SSO trelica](#configure-trelica-sso)** para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Crie um utilizador de teste Trelica](#create-a-trelica-test-user)** para ter uma contraparte de B.Simon em Trelica. Esta contrapartida está ligada à representação da AD Azure do utilizador.
1. **[Teste sSO](#test-sso)** para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **trelica,** vá para a secção **Gerir.** Selecione **um único sinal .**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone do lápis para **configuração Básica do SAML** para editar as definições.

   ![Configurar um único sign-on com a página SAML, com o ícone do lápis para configuração Básica sAML em destaque](common/edit-urls.png)

1. No **set up Single Sign-on com** a página SAML, introduza os seguintes valores:

    1. Na caixa **identificador,** introduza o URL **https://app.trelica.com** .

    1. Na caixa **DEURL resposta,** introduza um URL com o padrão `https://app.trelica.com/Id/Saml2/<CUSTOM_IDENTIFIER>/Acs` .

    > [!NOTE]
    > O valor url de **resposta** não é real. Atualize este valor com o URL de resposta real. Contacte a equipa de apoio ao [Cliente Trelica](mailto:support@trelica.com) para obter este valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. No **set set Single Sign-on com** a página SAML, vá à secção de Certificado de **Assinatura SAML.** À direita do Url de Metadados da Federação de **Aplicações,** selecione o botão de cópia para copiar o URL. Guarde o URL no seu computador.

    ![A secção de Certificado de Assinatura SAML, com o botão de cópia realçado ao lado do Url de Metadados da Federação de Aplicações](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, cria-se um utilizador de teste chamado B.Simon no portal Azure.

1. No painel mais à esquerda do portal Azure, selecione **Utilizadores de Diretório Ativo Azure**  >  **Users**  >  **Todos os utilizadores**.
1. Na parte superior do ecrã, selecione **Novo utilizador**.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome,** insira **B.Simon.**
   1. No campo de **nome do Utilizador,** introduza o domínio da_empresa_ **B.Simon@****.** _extensão_. Por exemplo, B.Simon@contoso.com.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está mostrado na caixa **password.**
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que b.Simon utilize um único sign-on Azure, concedendo acesso à Trelica.

1. No portal Azure, selecione **Aplicações Empresariais**  >  **Todas as aplicações**.
1. Na lista de aplicações, selecione **Trelica**.
1. Na página geral da aplicação, vá à secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A secção Gerir, com Utilizadores e grupos em destaque](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**. Na caixa de diálogo **Adicionar Atribuição,** selecione **Utilizadores e grupos**.

   ![A janela utilizadores e grupos, com adicionar utilizador em destaque](common/add-assign-user.png)

1. Na caixa de diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de utilizadores. Em seguida, escolha o botão **Select** na parte inferior do ecrã.
1. Se esperar algum valor de papel na afirmação do SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista. Em seguida, escolha o botão **Select** na parte inferior do ecrã.
1. Na caixa de diálogo **Adicionar Atribuição,** selecione **Atribuir**.

## <a name="configure-trelica-sso"></a>Configure Trelica SSO

Para configurar um único sinal no lado **trelica,** envie o valor de url de url de metadados da Federação de **Aplicações** copiado para a equipa de [suporte Trelica](mailto:support@trelica.com). Configuram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

### <a name="create-a-trelica-test-user"></a>Criar um utilizador de teste Trelica

Nesta secção, cria-se um utilizador chamado B.Simon em Trelica.

A Trelica suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há ação para tomar nesta secção. Se um utilizador já não existir na Trelica, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de Acesso AD Azure utilizando o portal My Apps.

Ao selecionar o azulejo Trelica no portal My Apps, é automaticamente inscrito na Trelica para a qual configura o SSO. Para mais informações sobre o portal My Apps, consulte [introdução ao portal My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais para integrar aplicações SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente Trelica com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger a Trelica com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
