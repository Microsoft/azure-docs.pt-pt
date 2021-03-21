---
title: 'Tutorial: Integração do Diretório Ativo Azure com O.C. Tanner - AppreciateHub | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o O.C. Tanner - AppreciateHub.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/28/2020
ms.author: jeedes
ms.openlocfilehash: 683cfc65e8154d4f409f5d4a33bf7ccf61c6f7c6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92518590"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-oc-tanner---appreciatehub"></a>Tutorial: Azure Ative Directory integração única de sign-on (SSO) com O.C. Tanner - AppreciateHub

Neste tutorial, aprenderá a integrar o O.C. Tanner - AppreciateHub com Azure Ative Directory (Azure AD). Quando integrares o O.C. Tanner - AppreciateHub com Azure AD, você pode:

* Controlo em Azure AD que tem acesso a O.C. Tanner - AppreciateHub.
* Ative os seus utilizadores para serem automaticamente inscritos no O.C. Tanner - AppreciateHub com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* O.C. Tanner - Subscrição única do AppreciateHub (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* O.C. Tanner - AppreciateHub suporta **IDP** iniciado SSO

* Uma vez configurar o O.C. Tanner - AppreciateHub pode impor controlos de sessão, que protegem a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controlos de sessão estendem-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-oc-tanner---appreciatehub-from-the-gallery"></a>Adicionando O.C. Tanner - AppreciateHub da galeria

Para configurar a integração de O.C. Tanner - AppreciateHub em Azure AD, você precisa adicionar O.C. Tanner - AppreciateHub da galeria para a sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add da secção **de galeria,** tipo **O.C. Tanner - AppreciateHub** na caixa de pesquisa.
1. Selecione **O.C. Tanner - AppreciateHub** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-oc-tanner---appreciatehub"></a>Configure e teste Azure AD único sinal de inscrição para O.C. Tanner - AppreciateHub

Configure e teste Azure AD SSO com O.C. Tanner - AppreciateHub usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em O.C. Tanner - AppreciateHub.

Para configurar e testar Azure AD SSO com O.C. Tanner - AppreciateHub, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure O.C. Tanner - AppreciateHub SSO](#configure-oc-tanner---appreciatehub-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create O.C. Tanner - Utilizador de teste AppreciateHub](#create-oc-tanner---appreciatehub-test-user)** - para ter uma contrapartida de B.Simon em O.C. Tanner - AppreciateHub que está ligado à representação Azure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **O.C. Tanner - AppreciateHub,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **De Configuração Básica SAML,** o utilizador não tem de realizar qualquer passo, uma vez que a aplicação já está pré-integrada com o Azure.

1. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configuração O.C. Tanner - AppreciateHub,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, permitindo o acesso a O.C. Tanner - AppreciateHub.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **O.C. Tanner - AppreciateHub**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-oc-tanner---appreciatehub-sso"></a>Configuure O.C. Tanner - AppreciateHub SSO

Para configurar um único sign-on em **O.C. Tanner - Do** lado do AppreciateHub, é necessário enviar os **metadados XML da Federação** descarregados e URLs copiados apropriados do portal Azure para [O.C. Tanner - Equipa de apoio do AppreciateHub](mailto:sso@octanner.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-oc-tanner---appreciatehub-test-user"></a>Criar O.C. Tanner - Utilizador de teste AppreciateHub

O objetivo desta secção é criar um utilizador chamado Britta Simon em O.C. Tanner - AppreciateHub.

**Para criar um utilizador chamado Britta Simon in O.C. Tanner - AppreciateHub, execute os seguintes passos:**

Peça à sua [equipa de suporte O.C. Tanner - AppreciateHub](mailto:sso@octanner.com) para criar um utilizador que tenha como nomeID atribuir o mesmo valor que o nome de utilizador de Britta Simon em Azure AD.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no O.C. Tanner - Telha AppreciateHub no Painel de Acesso, você deve ser automaticamente inscrito no O.C. Tanner - AppreciateHub para o qual criou o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente O.C. Tanner - AppreciateHub com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger O.C. Tanner - AppreciateHub com visibilidade e controlos avançados](/cloud-app-security/proxy-intro-aad)