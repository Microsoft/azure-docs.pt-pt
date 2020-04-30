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
ms.openlocfilehash: 2e27d020bd25f234d084fba770e234bdccb40a99
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81682382"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-qualtrics"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com A Qualtrics SAP

Neste tutorial, você vai aprender a integrar a SAP Qualtrics com o Azure Ative Directory (Azure AD). Quando integrar a SAP Qualtrics com a Azure AD, pode:

* Controlo em Azure AD que tem acesso à SAP Qualtrics.
* Ative que os seus utilizadores sejam automaticamente inscritos na SAP Qualtrics com as suas contas Azure AD.
* Gerencie as suas contas num local central: o portal Azure.

Para saber mais sobre software como uma integração de aplicações de serviço (SaaS) com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa do seguinte:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma subscrição SAP Qualtrics ativada para um único sinal (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* A SAP Qualtrics suporta **SP** e **IDP** iniciado SSO.
* A SAP Qualtrics suporta o fornecimento de utilizadores **Just In Time.**
* Depois de configurar a SAP Qualtrics, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o acesso condicional. Para mais informações, consulte Saiba como impor o controlo da [sessão com](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)o Microsoft Cloud App Security .

## <a name="add-sap-qualtrics-from-the-gallery"></a>Adicione A Qualtrics SAP da galeria

Para configurar a integração da SAP Qualtrics em Azure AD, precisa adicionar AAp Qualtrics da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta de trabalho ou de escola, ou uma conta pessoal da Microsoft.
1. No painel esquerdo, selecione **Azure Ative Directory**.
1. Vá a **Aplicações Empresariais**e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, digite **A Qualtrics SAP** na caixa de pesquisa.
1. Selecione **SAP Qualtrics** a partir dos resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-qualtrics"></a>Configure e teste Azure AD único signo para SAP Qualtrics

Configure e teste Azure AD SSO com SAP Qualtrics, utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação ligada entre um utilizador da AD Azure e o utilizador relacionado na SAP Qualtrics.

Para configurar e testar o Azure AD SSO com a SAP Qualtrics, complete os seguintes blocos de construção:

1. [Configure O SSO AD Azure](#configure-azure-ad-sso) para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. [Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user) para testar o único sinal de Azure AD com B.Simon.
    1. [Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user) para permitir que b.Simon utilize um único sinal de AD Azure.
1. [Configure o SAP Qualtrics SSO](#configure-sap-qualtrics-sso) para configurar as definições de inscrição únicas no lado da aplicação.
    1. [Crie um utilizador de teste SAP Qualtrics](#create-sap-qualtrics-test-user) para ter uma contrapartida de B.Simon na SAP Qualtrics, ligada à representação da AD Azure do utilizador.
1. [Teste sSO](#test-sso) para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de **aplicações da SAP Qualtrics,** encontre a secção **Gerir.** Selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set set single sign-on com** a página SAML, selecione o ícone do lápis para **configuração SAML básica** para editar as definições.

   ![Screenshot de configurar um único sign-on com página SAML, com ícone de lápis realçado](common/edit-urls.png)

1. No set único sessão com a página **SAML,** se pretender configurar a aplicação no modo iniciado **idp,** introduza os valores para os seguintes campos:
    
    a. Na caixa de texto **identificador,** digite um URL que utilize o seguinte padrão:

    `https://< DATACENTER >.qualtrics.com`
   
    b. Na caixa de texto **URL de resposta,** digite um URL que utilize o seguinte padrão:

    `https://< DATACENTER >.qualtrics.com/login/v1/sso/saml2/default-sp`

    c. Na caixa de texto **do Estado relé,** digite um URL que utilize o seguinte padrão:

    `https://< brandID >.< DATACENTER >.qualtrics.com`

1. Selecione **Definir URLs adicionais**e executar o seguinte passo se pretender configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL que utilize o seguinte padrão:

    `https://< brandID >.< DATACENTER >.qualtrics.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de sign-on real, identificador, URL de resposta e estado de retransmissão. Para obter estes valores, contacte a equipa de suporte do [Cliente da Qualtrics.](https://www.qualtrics.com/support/) Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. No **set single sign-on com** a página SAML, na secção Certificado de **Assinatura SAML,** selecione o ícone de cópia para copiar o Url de **Metadados da Federação** de Aplicações e guarde-o no seu computador.

    ![Screenshot do Certificado de Assinatura SAML, com ícone de cópia em destaque](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, cria-se um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure,**Users** > selecione **Utilizadores de Diretório** > Ativo Azure**Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva a palavra-passe para baixo.
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que a B.Simon utilize um único sign-on Azure, concedendo acesso à SAP Qualtrics.

1. No portal Azure, selecione **Aplicações Empresariais** > **Todas as aplicações**.
1. Na lista de aplicações, selecione **SAP Qualtrics**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![Screenshot da secção Gerir, com Utilizadores e grupos em destaque](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**. Em seguida, na caixa de diálogo **adicionar atribuição,** selecione **Utilizadores e grupos**.

    ![Screenshot de Utilizadores e página de grupos, com adicionar utilizador destacado](common/add-assign-user.png)

1. Na caixa de diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de utilizadores. Em seguida, escolha **Selecionar** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de papel na afirmação do SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista. Em seguida, escolha **Selecionar** na parte inferior do ecrã.
1. Na caixa de diálogo **Adicionar Atribuição,** selecione **Atribuir**.

## <a name="configure-sap-qualtrics-sso"></a>Configure SAP Qualtrics SSO

Para configurar um único sinal no lado da Qualtrics SAP, envie o url copiado da Federação de Metadados da Federação de **Aplicações** do portal Azure para a equipa de suporte da [Qualtrics SAP](https://www.qualtrics.com/support/). A equipa de apoio garante que a ligação SAML SSO está corretamente definida em ambos os lados.

### <a name="create-sap-qualtrics-test-user"></a>Criar o utilizador de teste SAP Qualtrics

A SAP Qualtrics suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhuma ação adicional a tomar. Se um utilizador já não existir na SAP Qualtrics, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de acesso do Azure AD utilizando o Painel de Acesso.

Quando selecionar o azulejo SAP Qualtrics no Painel de Acesso, é automaticamente inscrito na Qualtrics SAP para a qual configura o SSO. Para mais informações, consulte [o Iniciar Sessão e inicie aplicações a partir do portal My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Tutoriais para integrar aplicações SaaS no Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente a SAP Qualtrics com AD Azure](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Proteja a Qualtrics SAP com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

