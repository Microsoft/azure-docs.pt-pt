---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com sistema de apoio à saúde | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Health Support System.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/29/2020
ms.author: jeedes
ms.openlocfilehash: 3bdd11fa8e083d3a36767c04fd20b72da2b544c5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92445008"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-health-support-system"></a>Tutorial: Azure Ative Directory integração única (SSO) com sistema de apoio à saúde

Neste tutorial, você vai aprender a integrar o Sistema de Apoio à Saúde com o Azure Ative Directory (Azure AD). Quando integrar o Sistema de Apoio à Saúde com Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Sistema de Apoio à Saúde.
* Capacitar os seus utilizadores a serem automaticamente inscritos no Sistema de Suporte à Saúde com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição ativada pelo Sistema de Suporte de Saúde (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Sistema de Apoio à Saúde apoia SSO iniciado **pela SP**

* Uma vez configurado o Sistema de Apoio à Saúde, pode impor controlos de sessão, que protegem a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controlos de sessão estendem-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-health-support-system-from-the-gallery"></a>Adicionar sistema de apoio à saúde da galeria

Para configurar a integração do Sistema de Apoio à Saúde em Azure AD, é necessário adicionar o Sistema de Apoio à Saúde da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** escreva **sistema de suporte à saúde** na caixa de pesquisa.
1. Selecione **o Sistema de Suporte à Saúde** a partir do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-health-support-system"></a>Configurar e testar Azure AD único sinal de saúde

Configure e teste Azure AD SSO com Sistema de Suporte à Saúde usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Sistema de Suporte à Saúde.

Para configurar e testar o Azure AD SSO com sistema de apoio à saúde, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Sistema de Suporte à Saúde SSO](#configure-health-support-system-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create Health Support System test user](#create-health-support-system-test-user)** - para ter uma contraparte de B.Simon no Sistema de Apoio à Saúde que está ligada à representação AZure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações do Sistema de Apoio à **Saúde,** encontre a secção **Gerir** e selecione **um único sinal**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** insira os valores para os seguintes campos: Na caixa de texto **URL sign-on,** digite o URL:  `https://suntory.karakoko.jp`
    
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
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Sistema de Apoio à Saúde.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Health Support System**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-health-support-system-sso"></a>Configure sistema de apoio à saúde SSO

Para configurar um único sinal no sistema de suporte à **saúde,** é necessário enviar o **Url de Metadados da Federação de Aplicações** para a equipa de suporte do Sistema de Apoio à [Saúde](https://wellcoms.jp/inquiry/). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-health-support-system-test-user"></a>Criar utilizador de teste do Sistema de Suporte à Saúde

Nesta secção, cria-se um utilizador chamado B.Simon in Health Support System. Trabalhe com a equipa de apoio ao Sistema de Apoio à [Saúde](https://wellcoms.jp/inquiry/) para adicionar os utilizadores na plataforma do Sistema de Apoio à Saúde. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo do Sistema de Apoio à Saúde no Painel de Acesso, deverá ser automaticamente inscrito no Sistema de Apoio à Saúde para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o Sistema de Apoio à Saúde com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger o Sistema de Apoio à Saúde com visibilidade e controlos avançados](/cloud-app-security/proxy-intro-aad)