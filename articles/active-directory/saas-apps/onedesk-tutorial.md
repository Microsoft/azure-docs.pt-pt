---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com a OneDesk | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o OneDesk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/18/2019
ms.author: jeedes
ms.openlocfilehash: 44d9bf446ff5d85c89bce729e17cbda129c8e4ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92518505"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-onedesk"></a>Tutorial: Azure Ative Directory integração única (SSO) com OneDesk

Neste tutorial, você vai aprender a integrar o OneDesk com o Azure Ative Directory (Azure AD). Quando integrar o OneDesk com a AD Azure, pode:

* Controlo em Azure AD que tem acesso ao OneDesk.
* Ative os seus utilizadores a serem automaticamente inscritos no OneDesk com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada por oneDesk (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* OneDesk apoia **SP e IDP** iniciado SSO
* OneDesk suporta **provisão de** utilizadores just in time

## <a name="adding-onedesk-from-the-gallery"></a>Adicionar OneDesk da galeria

Para configurar a integração do OneDesk no AD AZure, precisa adicionar o OneDesk da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **da galeria,** **digite OneDesk** na caixa de pesquisa.
1. Selecione **OneDesk** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-onedesk"></a>Configurar e testar Azure AD único sinal para OneDesk

Configure e teste Azure AD SSO com OneDesk usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no OneDesk.

Para configurar e testar o Azure AD SSO com o OneDesk, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o OneDesk SSO](#configure-onedesk-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create OneDesk test user](#create-onedesk-test-user)** - para ter uma contraparte de B.Simon em OneDesk que está ligada à representação AD Ad Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **OneDesk,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `onedesk.com_<specific_tenant_string>`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://app.onedesk.com/sso/saml/SSO/alias/onedesk.com_<specific_tenant_string>`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://app.onedesk.com/sso/saml/login/alias/onedesk.com_<specific_tenant_string>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte [a equipa de suporte do Cliente OneDesk](mailto:hello@onedesk.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção Configurar o **OneDesk,** copie os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao OneDesk.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **OneDesk**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-onedesk-sso"></a>Configurar o OneDesk SSO

1. Para automatizar a configuração dentro do OneDesk, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

1. Depois de adicionar extensão ao navegador, clique em **Configurar o OneDesk** irá direcioná-lo para a aplicação OneDesk. A partir daí, forneça as credenciais de administração para assinar no OneDesk. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-5.

    ![Configuração de configuração](common/setup-sso.png)

1. Se pretender configurar o OneDesk manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa OneDesk como administrador e execute os seguintes passos:

1. Clique no separador **Integrações.**

    ![Screenshot que mostra o separador "Integrações" selecionado.](./media/onedesk-tutorial/img-integration.png)

1. Clique no **Signo Único**, selecione **Carregar Ficheiro de Metadados** e clique no **Ficheiro Escolha** para carregar o ficheiro de metadados, que descarregou a partir do portal Azure.

    ![Separador de definições](./media/onedesk-tutorial/img-singlesignon.png)

### <a name="create-onedesk-test-user"></a>Criar utilizador de teste OneDesk

Nesta secção, um utilizador chamado B.Simon é criado no OneDesk. O OneDesk suporta o provisionamento do utilizador just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no OneDesk, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo OneDesk no Painel de Acesso, deverá ser automaticamente inscrito no OneDesk para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente oneDesk com Azure AD](https://aad.portal.azure.com/)