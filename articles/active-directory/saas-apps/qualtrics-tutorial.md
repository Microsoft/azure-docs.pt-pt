---
title: 'Tutorial: Integração do Diretório Ativo Azure com a SAP Qualtrics [ SAP Qualtrics] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o SAP Qualtrics.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4df889ab-2685-4d15-a163-1ba26567eeda
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/03/2020
ms.author: jeedes
ms.openlocfilehash: d7a35f9f366f45a111aa7111501c9261ecbfa87e
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2020
ms.locfileid: "80667507"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-qualtrics"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com A Qualtrics SAP

Neste tutorial, você vai aprender a integrar a SAP Qualtrics com o Azure Ative Directory (Azure AD). Quando integrar a SAP Qualtrics com a Azure AD, pode:

* Controlo em Azure AD que tem acesso à SAP Qualtrics.
* Ative que os seus utilizadores sejam automaticamente inscritos na SAP Qualtrics com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* SAP Qualtrics única subscrição ativada por SAP Qualtrics (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* SAP Qualtrics suporta **SP** e **IDP** iniciadoS SSO
* Suporte da Qualtrics SAP **Just In Time**
* Assim que configurar a SAP Qualtrics, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

## <a name="adding-sap-qualtrics-from-the-gallery"></a>Adicionando A EPDa da galeria

Para configurar a integração da SAP Qualtrics em Azure AD, precisa adicionar AAp Qualtrics da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, digite **A Qualtrics SAP** na caixa de pesquisa.
1. Selecione **SAP Qualtrics** do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-qualtrics"></a>Configure e teste Azure AD único signo para SAP Qualtrics

Configure e teste Azure AD SSO com SAP Qualtrics utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na SAP Qualtrics.

Para configurar e testar o Azure AD SSO com a SAP Qualtrics, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure sSO de qualtrics SAP](#configure-sap-qualtrics-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Crie um utilizador de teste SAP Qualtrics](#create-sap-qualtrics-test-user)** - para ter uma contrapartida de B.Simon na SAP Qualtrics que esteja ligada à representação da AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de **aplicações Da ApS Qualtrics,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. No set único sessão com a página **SAML,** Se desejar configurar a aplicação no modo iniciado do **IDP,** introduza os valores para os seguintes campos:
    
    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:

    `https://< DATACENTER >.qualtrics.com`
   
    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:

    `https://< DATACENTER >.qualtrics.com/login/v1/sso/saml2/default-sp`

    c. Na caixa de texto **do Estado relé,** digite um URL utilizando o seguinte padrão:

    `https://< brandID >.< DATACENTER >.qualtrics.com`

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    No **Sign on URL** textbox, digite um URL utilizando o seguinte padrão:

    `https://< brandID >.< DATACENTER >.qualtrics.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL, Identificador, URL de resposta e estado de retransmissão. Contacte a equipa de suporte do Cliente da [Qualtrics](https://www.qualtrics.com/support/) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. No **set single sign-on com** a página SAML, na secção Certificado de **Assinatura SAML,** clique no botão de cópia para copiar o Url de **Metadados da Federação** da Aplicação e guarde-o no seu computador.

    ![O link de descarregamento do Certificado](common/copy-metadataurl.png)

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

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso à SAP Qualtrics.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **SAP Qualtrics**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-sap-qualtrics-sso"></a>Configure SAP Qualtrics SSO

Para configurar um único sinal no lado **da SAP Qualtrics,** é necessário enviar o url copiado da Federação de Metadados da Federação de **Aplicações** do portal Azure para a equipa de [suporte da SAP Qualtrics](https://www.qualtrics.com/support/). Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

### <a name="create-sap-qualtrics-test-user"></a>Criar o utilizador de teste SAP Qualtrics

Nesta secção, um utilizador chamado Britta Simon é criado na SAP Qualtrics. A SAP Qualtrics suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir na SAP Qualtrics, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo SAP Qualtrics no Painel de Acesso, deve ser automaticamente inscrito na Qualtrics SAP para a qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente a SAP Qualtrics com AD Azure](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger a SAP Qualtrics com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

