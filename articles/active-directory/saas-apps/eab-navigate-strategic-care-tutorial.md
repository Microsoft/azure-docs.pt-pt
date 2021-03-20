---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com a EAB Navigate Strategic Care | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o EAB Navigate Strategic Care.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/20/2019
ms.author: jeedes
ms.openlocfilehash: d443f1f85caf53a40d5cd80b4f9438427d18a4fa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92454512"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-eab-navigate-strategic-care"></a>Tutorial: Azure Ative Directory integração única (SSO) com cuidados estratégicos da EAB Navigate

Neste tutorial, você aprenderá a integrar o EAB Navigate Strategic Care com o Azure Ative Directory (Azure AD). Quando integrar o EAB Navigate Strategic Care com Azure AD, pode:

* Controle em Azure AD que tem acesso a EAB Navigate Strategic Care.
* Capacitar os seus utilizadores a serem automaticamente inscritos na EAB Navigate Strategic Care com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* EAB Navigate Strategic Care única subscrição ativa (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* EAB Navigate Strategic Care suporta **SSO** iniciado SP

## <a name="adding-eab-navigate-strategic-care-from-the-gallery"></a>Adicionar EAB Navigate Strategic Care da galeria

Para configurar a integração do EAB Navigate Strategic Care em AD Azure, precisa adicionar eAB Navigate Strategic Care da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** escreva **EAB Navigate Strategic Care** na caixa de pesquisa.
1. Selecione **EAB Navigate Strategic Care** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-eab-navigate-strategic-care"></a>Configurar e testar Azure AD único sinal de acesso para cuidados estratégicos da EAB Navigate

Configure e teste Azure AD SSO com EAB Navigate Strategic Care usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na EAB Navigate Strategic Care.

Para configurar e testar o Azure AD SSO com a EAB Navigate Strategic Care, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o EAB Navigate Strategic Care SSO](#configure-eab-navigate-strategic-care-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create EAB Navigate Strategic Care test user](#create-eab-navigate-strategic-care-test-user)** - para ter uma contrapartida de B.Simon em EAB Navigate Strategic Care que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração da aplicação **EAB Navigate Strategic Care,** encontre a secção **Gerir** e selecione um único sinal de **saúde.**
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<CUSTOMERURL>.eab.com`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de Sign-On real. Contacte [a equipa de suporte do Cliente de Cuidados Estratégicos da EAB Para](mailto:tech@gradesfirst.com) obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à EAB Navigate Strategic Care.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **EAB Navigate Strategic Care**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-eab-navigate-strategic-care-sso"></a>Configurar eAB Navegar em Cuidados Estratégicos SSO

Para configurar um único sinal no lado **dos cuidados estratégicos da EAB Navigate,** é necessário enviar o **Url de Metadados da Federação de Aplicações** para a equipa de suporte de [cuidados estratégicos da EAB Navigate.](mailto:tech@gradesfirst.com) Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-eab-navigate-strategic-care-test-user"></a>Criar utilizador de teste de cuidados estratégicos EAB Navigate

Nesta secção, cria-se um utilizador chamado B.Simon in EAB Navigate Strategic Care. Trabalhe com a [equipa de suporte da EAB Navigate Strategic Care](mailto:tech@gradesfirst.com) para adicionar os utilizadores na plataforma EAB Navigate Strategic Care. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo EAB Navigate Strategic Care no Painel de Acesso, deverá ser automaticamente inscrito no EAB Navigate Strategic Care para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o EAB Navigate Strategic Care com Azure AD](https://aad.portal.azure.com/)