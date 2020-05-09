---
title: 'Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com a Smart Global Governance [ Smart Global Governance ] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Smart Global Governance.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5c31613e-f30d-47d9-af51-001345b6db10
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 05/04/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb14f4c8dd498bf26ac093f7a491876cd2e73940
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872636"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-smart-global-governance"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com a Smart Global Governance

Neste tutorial, aprenderá a integrar a Smart Global Governance com o Azure Ative Directory (Azure AD). Quando integrar a Smart Global Governance com a Azure AD, pode:

* Controlo em Azure AD que tem acesso à Smart Global Governance.
* Permita que os seus utilizadores sejam automaticamente inscritos na Smart Global Governance com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura ativada pela Smart Global Governance (SSO) permitiu a subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Smart Global Governance apoia **SP e IDP** iniciadoS SSO
* Assim que configurar o Smart Global Governance, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

## <a name="adding-smart-global-governance-from-the-gallery"></a>Adicionando a Smart Global Governance da galeria

Para configurar a integração da Smart Global Governance em Azure AD, você precisa adicionar smart Global Governance da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **de galeria,** digite **Smart Global Governance** na caixa de pesquisa.
1. Selecione **Smart Global Governance** a partir do painel de resultados e, em seguida, adicione a app. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-smart-global-governance"></a>Configure e teste Azure AD único sign-on para Smart Global Governance

Configure e teste Azure AD SSO com Smart Global Governance usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Smart Global Governance.

Para configurar e testar o Azure AD SSO com a Smart Global Governance, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure smart Global Governance SSO](#configure-smart-global-governance-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Create Smart Global Governance user](#create-smart-global-governance-test-user)** - para ter uma contrapartida de B.Simon na Smart Global Governance que está ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **smart Global Governance,** encontre a secção **Gerir** e selecione um **único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** caso deseje configurar a aplicação no modo iniciado do **IDP,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **identificador,** utilize um dos seguintes URL:

    | | |
    |-|-|
    | `https://eu-fr-south.console.smartglobalprivacy.com/platform/authentication-saml2/metadata`|
    | `https://eu-fr-south.console.smartglobalprivacy.com/dpo/authentication-saml2/metadata`|

    b. Na caixa de texto **URL resposta,** utilize um dos seguintes URL:

    | | |
    |-|-|
    | `https://eu-fr-south.console.smartglobalprivacy.com/platform/authentication-saml2/acs`|
    | `https://eu-fr-south.console.smartglobalprivacy.com/dpo/authentication-saml2/acs`|

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **"Sign-on URL",** utilize um dos seguintes URL:

    | | |
    |-|-|
    | `https://eu-fr-south.console.smartglobalprivacy.com/dpo`|
    | `https://eu-fr-south.console.smartglobalprivacy.com/platform`|

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **certificado (Cru)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificateraw.png)

1. Na secção **De Configuração de Governança Global Inteligente,** copie os URL(s) adequados com base na sua exigência.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que b.Simon use o único sign-on azure, concedendo acesso à Smart Global Governance.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Smart Global Governance**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-smart-global-governance-sso"></a>Configure Smart Global Governance SSO

Para configurar um único sign-on no lado smart **Global Governance,** você precisa enviar o Certificado descarregado **(Raw)** e URLs copiados apropriados do portal Azure para a equipa de suporte smart [Global Governance](mailto:support.tech@smartglobal.com). Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

### <a name="create-smart-global-governance-test-user"></a>Criar o utilizador de teste smart Global Governance

Nesta secção, cria-se uma utilizadora chamada Britta Simon na Smart Global Governance. Trabalhe com a equipa de suporte smart [Global Governance](mailto:support.tech@smartglobal.com) para adicionar os utilizadores na plataforma Smart Global Governance. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Smart Global Governance no Painel de Acesso, deve ser automaticamente inscrito no Smart Global Governance para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente a Smart Global Governance com a Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger a Smart Global Governance com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)