---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com a Comunidade Spark Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Community Spark.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: cbe52427-93fa-4d19-a958-7585e01f155d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 363dc6bdf8339379b17f1672c46ac38b856cf0b9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87101758"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-community-spark"></a>Tutorial: Azure Ative Directory integração única (SSO) com a Community Spark

Neste tutorial, você vai aprender a integrar a Community Spark com O Azure Ative Directory (Azure AD). Quando integrar a Community Spark com Azure AD, pode:

* Controlo em Azure AD que tem acesso à Faísca Comunitária.
* Permita que os seus utilizadores sejam automaticamente inscritos na Community Spark com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada por Spark Único (SSO) da Comunidade Spark.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Community Spark apoia SSO iniciado **pela SP**

* Community Spark apoia o fornecimento **de utilizadores just in time**

* Uma vez configurado o Community Spark, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-community-spark-from-the-gallery"></a>Adicionando faísca comunitária da galeria

Para configurar a integração da Community Spark em Azure AD, você precisa adicionar a Faísca Comunitária da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite Faísca Comunitária** na caixa de pesquisa.
1. Selecione **a Faísca Comunitária** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-community-spark"></a>Configure e teste Azure AD SSO para faísca comunitária

Configure e teste Azure AD SSO com a Community Spark utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na Community Spark.

Para configurar e testar o Azure AD SSO com a Faísca Comunitária, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure a Comunidade Spark SSO](#configure-community-spark-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Community Spark test user](#create-community-spark-test-user)** - para ter uma contrapartida de B.Simon em Faísca Comunitária que está ligada à representação Azure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Community Spark,** encontre a secção **Gerir** e selecione **um único sinal**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão:`https://<SUBDOMAIN>.communityspark.co/`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão:`https://<SUBDOMAIN>.communityspark.co/saml/metadata`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<SUBDOMAIN>.communityspark.co/saml/consume`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real em URL, URL de resposta e identificador. Contacte [a equipa de suporte do Cliente Community Spark](mailto:support@socialassurance.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

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
   1. Clique em **Create** (Criar).

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Faísca Comunitária.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Community Spark**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-community-spark-sso"></a>Configurar a Comunidade Faísca SSO

Para configurar um único sinal no lado **da Faísca Comunitária,** é necessário enviar o **Url de Metadados da Federação de Aplicações** para a [equipa de suporte da Faísca Comunitária.](mailto:support@socialassurance.com) Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-community-spark-test-user"></a>Criar utilizador de teste de faísca comunitária

Nesta secção, um utilizador chamado B.Simon é criado em Community Spark. A Community Spark suporta o provisionamento do utilizador just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir na Community Spark, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo comunitário do Spark no Painel de Acesso, deverá ser automaticamente inscrito na Faísca Comunitária para a qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente a Faísca Comunitária com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger a Faísca Comunitária com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

