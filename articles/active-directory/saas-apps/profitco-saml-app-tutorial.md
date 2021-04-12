---
title: 'Tutorial: Azure Ative Directory integração única (SSO) com Profit.co | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Profit.co.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/30/2020
ms.author: jeedes
ms.openlocfilehash: 027fb66538dfcead24470507b8080f2e0aacb469
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92515207"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-profitco"></a>Tutorial: Azure Ative Directory integração única de sign-on (SSO) com Profit.co

Neste tutorial, você vai aprender a integrar Profit.co com Azure Ative Direy (Azure AD). Quando integra Profit.co com a AD Azure, pode:

* Controlo em Azure AD que tem acesso a Profit.co.
* Permita que os seus utilizadores sejam automaticamente inscritos para Profit.co com as suas contas AD Azure.
* Gerencie as suas contas numa localização central, o portal Azure.

Para saber mais sobre software como integração de aplicações de serviço (SaaS) com Azure AD, consulte O que é o acesso à [aplicação e um único acesso com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Profit.co subscrição ativada por um único sso (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Profit.co apoia o IDP iniciado SSO.

* Depois de configurar Profit.co, pode impor o controlo da sessão. Isto protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o acesso condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-profitco-from-the-gallery"></a>Adicione Profit.co da galeria

Para configurar a integração de Profit.co no AD Azure, é necessário adicionar Profit.co da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta de trabalho ou escola ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Vá a **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** escreva **Profit.co** na caixa de pesquisa.
1. Selecione **Profit.co** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-profitco"></a>Configurar e testar a Azure AD um único sinal de Profit.co

Configure e teste Azure AD SSO com Profit.co utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, estabeleça uma relação ligada entre um utilizador Azure AD e o utilizador relacionado em Profit.co.

Aqui estão os passos gerais para configurar e testar Azure AD SSO com Profit.co:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user)** para testar o Azure AD com B.Simon.
    1. **[Atribua ao utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que a B.Simon utilize um único sinal de Ad AD.
1. **[Configurar Profit.co SSO](#configure-profitco-sso)** para configurar as definições de inscrição única no lado da aplicação.
    1. **[Crie Profit.co utilizador de teste](#create-a-profitco-test-user)** para ter uma contraparte de B.Simon em Profit.co. Esta contrapartida está ligada à representação Azure AD do utilizador.
1. **[Teste SSO](#test-sso)** para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Profit.co,** encontre a secção **Gerir.** Selecione **um único sinal de inscrição**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, selecione o ícone de lápis para **Configuração SAML Básica** para editar as definições.

   ![Screenshot de Configurar um único sinal com página SAML, com ícone de lápis em destaque](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** a aplicação está pré-configurada e os URLs necessários já estão pré-pré-levantados em Azure. Os utilizadores precisam de guardar a configuração selecionando o botão **Guardar.**

1. Na **configuração de um único sinal de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** selecione o botão **Copiar.** Isto copia o **url de metadados da Federação de Aplicações** e guarda-o no seu computador.

    ![Screenshot do Certificado de Assinatura SAML, com o botão de cópia realçado](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, cria-se um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Utilizadores do Diretório Ativo Azure**  >    >  **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavras-passe Show** e, em seguida, anote o valor mostrado no campo **Palavra-passe.**
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que B.Simon utilize o Azure single sign-on, permitindo o acesso a Profit.co.

1. No portal Azure, selecione **Aplicações empresariais**  >  **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Profit.co**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![Screenshot da secção Gerir, com utilizadores e grupos em destaque](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**. Na caixa de diálogo **de atribuição de** adicionar, selecione **Utilizadores e grupos**.

    ![Screenshot da página de Utilizadores e grupos, com o utilizador Adicionar em destaque](common/add-assign-user.png)

1. Na caixa de diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de utilizadores. Em seguida, escolha o botão **Selecione** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista. Em seguida, escolha o botão **Selecione** na parte inferior do ecrã.
1. Na caixa de diálogo **'Adicionar Atribuição',** selecione **Atribuir**.

## <a name="configure-profitco-sso"></a>Configurar Profit.co SSO

Para configurar um único sinal no lado Profit.co, é necessário enviar o URL de Metadados da Federação de Aplicações para a [equipa de suporte Profit.co](mailto:support@profit.co). Configuram esta definição para que a ligação SSO SAML seja corretamente definida em ambos os lados.

### <a name="create-a-profitco-test-user"></a>Criar um utilizador de teste Profit.co

Nesta secção, cria-se um utilizador chamado B.Simon em Profit.co. Trabalhe com a [equipa de suporte Profit.co](mailto:support@profit.co) para adicionar os utilizadores na plataforma Profit.co. Não pode utilizar uma única sessão de inscrição até criar e ativar os utilizadores.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de acesso Azure AD utilizando o Painel de Acesso.

Quando selecionar o azulejo Profit.co no Painel de Acesso, deverá ser automaticamente inscrito no Profit.co para o qual configura sSO. Para mais informações, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Tutorials for integrating SaaS applications with Azure Active Directory](./tutorial-list.md) (Tutoriais para integrar aplicações SaaS no Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente Profit.co com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger Profit.co com visibilidade e controlos avançados](/cloud-app-security/proxy-intro-aad)