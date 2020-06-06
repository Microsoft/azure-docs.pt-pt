---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com Helix Core - Helix Authentication Service / Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Helix Core - Helix Authentication Service.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 70625d03-2156-4dea-828b-0af251e12db9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/03/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: afe752dbaffc96186d37bfae319367e2c6e13385
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/06/2020
ms.locfileid: "84466158"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-helix-core---helix-authentication-service"></a>Tutorial: Azure Ative Directory integração única (SSO) com Helix Core - Helix Authentication Service

Neste tutorial, você vai aprender a integrar o Helix Core - Helix Authentication Service com Azure Ative Directory (Azure AD). Quando integrar o Helix Core - Serviço de Autenticação Helix com AZure AD, pode:

* Controlo em Azure AD que tem acesso ao Helix Core - Helix Authentication Service.
* Ative os seus utilizadores a serem automaticamente inscritos no Serviço de Autenticação Helix Core - Helix com as suas contas AD AZure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Helix Core - Helix Authentication Service única assinatura ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Helix Core - Serviço de Autenticação Helix suporta **SSO** iniciado SP
* Assim que configurar o Serviço de Autenticação Helix Core - Helix, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-helix-core---helix-authentication-service-from-the-gallery"></a>Adicionar Helix Core - Serviço de Autenticação Helix da galeria

Para configurar a integração do Serviço de Autenticação Helix Core - Helix em AD AZure, é necessário adicionar o Serviço de Autenticação Helix Core - Helix da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **escreva Helix Core - Serviço de Autenticação Helix** na caixa de pesquisa.
1. Selecione Helix Core - Serviço de **Autenticação Helix** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-helix-core---helix-authentication-service"></a>Configurar e testar a Azure AD um único sinal de inscrição para o Helix Core - Serviço de Autenticação Helix

Configure e teste Azure AD SSO com Helix Core - Helix Authentication Service usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Serviço de Autenticação Helix Core - Helix.

Para configurar e testar o Azure AD SSO com o Helix Core - Helix Authentication Service, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Helix Core - Helix Authentication Service SSO](#configure-helix-core-helix-authentication-service-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Criar o Helix Core - Utilizador de teste de autenticação helix](#create-helix-core-helix-authentication-service-test-user)** - para ter uma contrapartida de B.Simon in Helix Core - Helix Authentication Service que está ligada à representação AD AZure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Helix Core - Helix Authentication Service,** encontre a secção **Gerir** e selecione **um único sinal**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão:`https://<helix-auth-service>.<customer-hostname>.com/`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão:`https://<helix-auth-service>.<customer-hostname>.com/saml`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de suporte do cliente helix Core - Helix Authentication Service](mailto:support@perforce.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

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
   1. Clique **em Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon utilize o Azure single sign-on, concedendo acesso ao Serviço de Autenticação Helix Core - Helix.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Helix Core - Helix Authentication Service**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-helix-core-helix-authentication-service-sso"></a>Configure helix core-helix serviço de autenticação SSO

Para configurar um único sinal no lado helix Core - Serviço de **Autenticação Helix,** é necessário enviar o **Url de Metadados da Federação de Aplicações** para a equipa de suporte do Serviço de [Autenticação Helix Core](mailto:support@perforce.com)- Helix . Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-helix-core-helix-authentication-service-test-user"></a>Criar utilizador de teste de autenticação Hélice Core-Helix

Nesta secção, cria-se um utilizador chamado Britta Simon in Helix Core - Helix Authentication Service. Trabalhe com [a equipa de suporte do Helix Core - Helix Authentication Service](mailto:support@perforce.com) para adicionar os utilizadores na plataforma Helix Core - Helix Authentication Service. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo helix Core - Helix Authentication Service no Painel de Acesso, deverá ser automaticamente inscrito no Serviço de Autenticação Helix Core - Helix para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Helix Core - Serviço de Autenticação Helix com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger o Helix Core - Serviço de Autenticação Helix com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

