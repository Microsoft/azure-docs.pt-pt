---
title: 'Tutorial: Azure Ative Diretório integração única (SSO) com sistema de apoio à saúde  Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Health Support System.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 96f35ded-07bc-46f0-87df-fe88fcc367cd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/29/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7cf449880c1a7d1fdd1f951bf5ecbb7316efb8e8
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76992339"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-health-support-system"></a>Tutorial: Azure Ative Diretório integração única (SSO) com sistema de suporte de saúde

Neste tutorial, você vai aprender a integrar o Sistema de Apoio à Saúde com o Azure Ative Directory (Azure AD). Quando integra o Sistema de Apoio à Saúde com a AD Azure, pode:

* Controlo em Azure AD que tem acesso ao Sistema de Apoio à Saúde.
* Ative que os seus utilizadores sejam automaticamente inscritos no Sistema de Suporte de Saúde com as suas contas Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* O sistema de suporte de saúde ativou a subscrição ativada pelo Sistema de Apoio à Saúde (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* Sistema de Apoio à Saúde apoia **SP** iniciado SSO

* Uma vez configurado o Sistema de Suporte de Saúde, pode impor controlos de sessão, que protegem a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controles de sessão se estendem do acesso condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

## <a name="adding-health-support-system-from-the-gallery"></a>Adicionar sistema de apoio à saúde da galeria

Para configurar a integração do Sistema de Suporte de Saúde em Azure AD, é necessário adicionar o Sistema de Apoio à Saúde da galeria à sua lista de aplicações geridas do SaaS.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. No Add da secção **galeria,** digite sistema de suporte de **saúde** na caixa de pesquisa.
1. Selecione o Sistema de **Suporte à Saúde** a partir do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-health-support-system"></a>Configure e teste Azure AD único sign-on para sistema de suporte de saúde

Configure e teste Azure AD SSO com sistema de suporte de saúde utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Sistema de Suporte à Saúde.

Para configurar e testar o Azure AD SSO com o Sistema de Apoio à Saúde, complete os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. Configure o Sistema de Suporte à **[Saúde SSO](#configure-health-support-system-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Create Health Support System -](#create-health-support-system-test-user)** para ter uma contrapartida de B.Simon no Sistema de Suporte à Saúde que está ligada à representação da AD Azure do utilizador.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações do Sistema de Suporte de **Saúde,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos: Na caixa de texto **url sign-on,** escreva o URL: `https://suntory.karakoko.jp`
    
1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique no botão Copiar para copiar a URL de **metadados de Federação do aplicativo** e salvá-la no computador.

    ![O link de download de certificado](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B. Simon.

1. No painel esquerdo na portal do Azure, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.
1. Selecione **novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **usuário** , siga estas etapas:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome de usuário** , insira o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **senha** .
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso ao Sistema de Apoio à Saúde.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de candidaturas, selecione **Sistema de Suporte à Saúde**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-health-support-system-sso"></a>Configure Sistema de Suporte à Saúde SSO

Para configurar um único login no lado do Sistema de Suporte de **Saúde,** você precisa enviar o Url de **Metadados da Federação de Aplicações** para a equipa de suporte do Sistema de Suporte à [Saúde](https://wellcoms.jp/inquiry/). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-health-support-system-test-user"></a>Criar o utilizador de teste do Sistema de Suporte à Saúde

Nesta secção, cria-se um utilizador chamado B.Simon no Sistema de Apoio à Saúde. Trabalhar com a equipa de suporte do Sistema de Apoio à [Saúde](https://wellcoms.jp/inquiry/) para adicionar os utilizadores na plataforma Do Sistema de Apoio à Saúde. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clicar no azulejo do Sistema de Suporte de Saúde no Painel de Acesso, deverá ser automaticamente inscrito no Sistema de Apoio à Saúde para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Sistema de Apoio à Saúde com AD Azure](https://aad.portal.azure.com/)

- [O que é o controle de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger o Sistema de Apoio à Saúde com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)