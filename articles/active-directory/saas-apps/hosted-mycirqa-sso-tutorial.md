---
title: 'Tutorial: Azure Ative Directy integração única de sign-on (SSO) com O MyCirqa SSO hospedado Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o MyCirqa SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/26/2019
ms.author: jeedes
ms.openlocfilehash: 06918f6d9c62624915be327b61aa6a3ee4e2b12a
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92442763"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hosted-mycirqa-sso"></a>Tutorial: Azure Ative Directory integração única de sign-on (SSO) com MyCirqa SSO hospedado

Neste tutorial, você vai aprender a integrar o MyCirqa SSO hospedado com Azure Ative Directory (Azure AD). Quando integrar o MyCirqa SSO hospedado com Azure AD, pode:

* Control em Azure AD que tem acesso a MyCirqa SSO hospedado.
* Permita que os seus utilizadores sejam automaticamente inscritos no MyCirqa SSO hospedado com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Acolheu a subscrição ativada por MyCirqa SSO (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Hosted MyCirqa SSO suporta **SP** iniciado SSO

## <a name="adding-hosted-mycirqa-sso-from-the-gallery"></a>Adicionando MyCirqa SSO hospedado da galeria

Para configurar a integração do MyCirqa SSO em AD Azure, precisa adicionar O MyCirqa SSO hospedado da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add a partir da secção **da galeria,** **escreva MyCirqa SSO** na caixa de pesquisa.
1. Selecione **O MyCirqa SSO hospedado** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-hosted-mycirqa-sso"></a>Configurar e testar Azure AD único sign-on para Hosted MyCirqa SSO

Configure e teste Azure AD SSO com Hosted MyCirqa SSO usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no MyCirqa SSO hospedado.

Para configurar e testar o Azure AD SSO com o MyCirqa SSO hospedado, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o MyCirqa SSO](#configure-hosted-mycirqa-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create Hosted MyCirqa SSO test user](#create-hosted-mycirqa-sso-test-user)** - para ter uma contraparte de B.Simon em Hosted MyCirqa SSO que está ligada à representação AD Ad Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **MyCirqa SSO,** encontre a secção **Gestão** e selecione **um único sinal de sessão**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.cirqahosting.com/CirqaIdentity/external?`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://isoxford.com/<CUSTOMID>/cirqaidentity/saml2`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de apoio ao cliente MyCirqa SSO](not sure) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a MyCirqa SSO hospedado.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, **selecione Hosted MyCirqa SSO**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-hosted-mycirqa-sso"></a>Configure hospedado MyCirqa SSO

Para configurar um único sign-on no lado **Do MyCirqa SSO hospedado,** você precisa enviar o **url de metadados da Federação de Aplicações** para hospedar a equipe de [suporte MyCirqa SSO](mailto:support@isoxford.com)hospedado . Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

## <a name="create-hosted-mycirqa-sso-test-user"></a>Criar utilizadores de teste MyCirqa SSO hospedados

Nesta secção, cria-se um utilizador chamado Britta Simon in Hosted MyCirqa SSO. Trabalhe com [a equipa de suporte MyCirqa SSO do MyCirqa](mailto:support@isoxford.com) para adicionar os utilizadores na plataforma Hosted MyCirqa SSO. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo SSO do MyCirqa Hospedado no Painel de Acesso, deverá ser automaticamente inscrito no MyCirqa SSO do MyCirqa, para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o MyCirqa SSO hospedado com Azure AD](https://aad.portal.azure.com/)