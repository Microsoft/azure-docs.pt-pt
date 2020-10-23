---
title: 'Tutorial: Integração Azure AD SSO com Gestão de Processos de Mudança'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Change Process Management.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/07/2020
ms.author: jeedes
ms.openlocfilehash: 03c78f05566876356e4f486368dc2a5b3a29de43
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92456263"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-change-process-management"></a>Tutorial: Azure Ative Directy integração única (SSO) com a Change Process Management

Neste tutorial, você vai aprender como integrar a Change Process Management com o Azure Ative Directory (Azure AD). Quando integrar a Gestão de Processos de Mudança com AD Azure, pode:

* Use a Azure AD para controlar quem pode aceder à Gestão de Processos de Mudança.
* Permitir que os seus utilizadores sejam automaticamente inscritos na Change Process Management com as suas contas AD Azure.
* Gerencie as suas contas num local central: o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte [o Single sign-on para aplicações no Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma subscrição de Gestão de Processo de Alteração com um único sinal de sso ativado.

## <a name="tutorial-description"></a>Descrição tutorial

Neste tutorial, você vai configurar e testar Azure AD SSO em um ambiente de teste.

A Change Process Management suporta sSO iniciado pelo IDP.

Depois de configurar a Change Process Management, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controlos de sessão estendem-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-change-process-management-from-the-gallery"></a>Adicionar Gestão de Processos de Mudança da galeria

Para configurar a integração da Change Process Management em AD Azure, é necessário adicionar a Change Process Management da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) com uma conta de trabalho ou escola ou com uma conta pessoal da Microsoft.
1. No painel esquerdo, selecione **Azure Active Directory**.
1. Vá às **aplicações da Enterprise** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar uma aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** insira **a Gestão do Processo de Alteração** na caixa de pesquisa.
1. Selecione **Change Process Management** no painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-change-process-management"></a>Configurar e testar Azure AD SSO para gestão de processos de mudança

Irá configurar e testar O SSO Azure AD com Change Process Management utilizando um utilizador de teste chamado B.Simon. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador correspondente na Gestão de Processos de Mudança.

Para configurar e testar O SSO Azure AD com a Change Process Management, você vai dar estes passos de alto nível:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem a funcionalidade.
    1. **[Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user)** para testar o Azure AD com um único sinal de sessão.
    1. **[Conceder acesso ao utilizador de teste](#grant-access-to-the-test-user)** para permitir ao utilizador utilizar um único sinal de Azure.
1. **[Configure o SSO de Gestão de Processos de Alteração](#configure-change-process-management-sso)** no lado da aplicação.
    1. **[Crie um utilizador de teste de Gestão de Processos de Alteração](#create-a-change-process-management-test-user)** como contrapartida da representação AD do utilizador.
1. **[Teste SSO](#test-sso)** para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Change Process Management,** na secção **Gerir,** selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. Na **configuração de Sign-On única com** a página SAML, selecione o botão de lápis para **configuração SAML Básica** para editar as definições:

   ![Botão de lápis para configuração básica de SAML](common/edit-urls.png)

1. Na **configuração single Sign-On com página SAML,** tome estes passos:

    a. Na caixa **identifier,** introduza um URL no seguinte padrão: `https://<hostname>:8443/`

    b. Na caixa **URL de resposta,** introduza um URL no seguinte padrão: `https://<hostname>:8443/changepilot/saml/sso`

    > [!NOTE]
    > Os valores de URL **de identificação** e **resposta** anteriores não são os valores reais que deve utilizar. Contacte a [equipa de suporte para gestão de processos](mailto:support@realtech-us.com) de mudança para obter os valores reais. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** selecione o link **de descarregamento** para **Certificado (Base64)** para descarregar o certificado e guardá-lo no seu computador:

    ![Link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção De Gestão de **Processos de Alteração,** copie os URL ou URLs apropriados, com base nos seus requisitos:

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste chamado B.Simon no portal Azure.

1. No painel esquerdo do portal Azure, selecione **Azure Ative Directory**. Selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** complete estes passos:
   1. Na caixa **Nome,** insira **B.Simon**.  
   1. Na caixa **do nome do utilizador,** \<username> @ \<companydomain> introduza \<extension> . . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione **Mostrar a palavra-passe**e, em seguida, anotar o valor que é apresentado na caixa **palavra-passe.**
   1. Selecione **Criar**.

### <a name="grant-access-to-the-test-user"></a>Conceder acesso ao utilizador de teste

Nesta secção, você permitirá que B.Simon utilize o Azure single sign-on, concedendo a esse utilizador acesso à Gestão de Processos de Mudança.

1. No portal Azure, selecione **aplicações Enterprise**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Change Process Management**.
1. Na página geral da aplicação, na secção **Gerir,** selecione **Utilizadores e grupos**:

   ![Selecionar Utilizadores e grupos](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** na caixa de diálogo **'Adicionar Atribuição'.**

    ![Selecione Adicionar utilizador](common/add-assign-user.png)

1. Na caixa de diálogo **de Utilizadores e grupos,** selecione **B.Simon** na lista **de Utilizadores** e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Na caixa de diálogo **'Adicionar Atribuição',** selecione **Atribuir**.

## <a name="configure-change-process-management-sso"></a>Configurar alterar processo de gestão de processos SSO

Para configurar um único sinal no lado da Gestão do Processo de Alteração, é necessário enviar o certificado Base64 descarregado e os URLs apropriados que copiou do portal Azure para a equipa de suporte de Gestão de [Processos de Alteração.](mailto:support@realtech-us.com) Configuram a ligação SSO SAML para ser correta em ambos os lados.

### <a name="create-a-change-process-management-test-user"></a>Criar um utilizador de teste de Gestão de Processos de Alteração
 Trabalhe com a equipa de suporte de [Gestão de Processos de Mudança](mailto:support@realtech-us.com) para adicionar um utilizador chamado B.Simon na Gestão de Processos de Mudança. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, irá testar a sua configuração Azure AD SSO utilizando o Painel de Acesso.

Quando selecionar o azulejo de Gestão de Processos de Alteração no Painel de Acesso, deverá ser automaticamente inscrito na instância de Gestão de Processos de Alteração para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [o Painel de Introdução ao Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais sobre como integrar apps SaaS com Diretório Ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)

- [Tente alterar gestão de processos com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger a Gestão de Processos de Mudança com visibilidade e controlos avançados](/cloud-app-security/proxy-intro-aad)