---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com Screencast-O-Matic Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Screencast-O-Matic.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/15/2019
ms.author: jeedes
ms.openlocfilehash: eae60ba9aabe417e986c7637ce700302bfe6c7bf
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92676575"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-screencast-o-matic"></a>Tutorial: Azure Ative Directory integração única (SSO) com Screencast-O-Matic

Neste tutorial, você vai aprender a integrar Screencast-O-Matic com Azure Ative Directory (Azure AD). Quando integrar o Screencast-O-Matic com Azure AD, pode:

* Control em Azure AD que tem acesso ao Screencast-O-Matic.
* Ative os seus utilizadores a serem automaticamente inscritos no Screencast-O-Matic com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura ativada por Screencast-O-Matic (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Screencast-O-Matic suporta **SSO** iniciado SP
* Screencast-O-Matic suporta provisão do utilizador **Just In Time**

## <a name="adding-screencast-o-matic-from-the-gallery"></a>Adicionando Screencast-O-Matic da galeria

Para configurar a integração do Screencast-O-Matic em AD Azure, é necessário adicionar Screencast-O-Matic da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações** .
1. Para adicionar nova aplicação, selecione **Nova aplicação** .
1. Na secção Adicionar a partir da secção **de galeria,** **digite Screencast-O-Matic** na caixa de pesquisa.
1. Selecione **Screencast-O-Matic** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-screencast-o-matic"></a>Configurar e testar Azure AD único sinal para screencast-O-Matic

Configure e teste Azure AD SSO com Screencast-O-Matic usando um utilizador de teste chamado **B.Simon** . Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Screencast-O-Matic.

Para configurar e testar o Azure AD SSO com o Screencast-O-Matic, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Screencast-O-Matic SSO](#configure-screencast-o-matic-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create Screencast-O-Matic test user](#create-screencast-o-matic-test-user)** - para ter uma contraparte de B.Simon no Screencast-O-Matic que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Screencast-O-Matic,** encontre a secção **Gerir** e selecione **um único sinal de sação** .
1. Na página de método **de inscrição** única, selecione **SAML** .
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://screencast-o-matic.com/<InstanceName>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de Sign-On real. Contacte [a equipa de suporte do Cliente Screencast-O-Matic](mailto:support@screencast-o-matic.com) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configurar Screencast-O-Matic,** copie os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory** , selecione **Utilizadores** , e, em seguida, selecione **Todos os utilizadores** .
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome** , introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar** .

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Screencast-O-Matic.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações** .
1. Na lista de aplicações, selecione **Screencast-O-Matic** .
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos** .

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-screencast-o-matic-sso"></a>Configurar screencast-o-Matic SSO

1. Para automatizar a configuração dentro do Screencast-O-Matic, é necessário instalar a **extensão do navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão** .

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

1. Depois de adicionar extensão ao navegador, clique em **Configurar Screencast-O-Matic** irá direcioná-lo para a aplicação Screencast-O-Matic. A partir daí, forneça as credenciais de administração para assinar no Screencast-O-Matic. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-11.

    ![Configuração de configuração](common/setup-sso.png)

1. Se pretender configurar manualmente o Screencast-O-Matic, abra manualmente uma nova janela do navegador web e inscreva-se no site da empresa Screencast-O-Matic como administrador e execute os seguintes passos:

1. Clique na **Subscrição** .

    ![A Assinatura](./media/screencast-tutorial/tutorial_screencast_sub.png)

1. Na secção **página de acesso,** clique na **configuração** do clique .

    ![O Acesso](./media/screencast-tutorial/tutorial_screencast_setup.png)

1. Na página de acesso à **configuração,** execute os seguintes passos.

1. Na secção **URL de acesso,** digite o nome de instância na caixa de texto especificada.

    ![O Acesso](./media/screencast-tutorial/tutorial_screencast_access.png)

1. Selecione **Exigir utilizador de domínio** sob a secção de **restrição do utilizador SAML (opcional).**

1. Em **Upload IDP Metadata XML File** , Clique em Escolher **Ficheiro** para carregar os metadados que descarregou a partir do portal Azure.

1. Clique em **OK** .

    ![O Acesso](./media/screencast-tutorial/tutorial_screencast_save.png)

### <a name="create-screencast-o-matic-test-user"></a>Criar utilizador de teste Screencast-O-Matic

Nesta secção, um utilizador chamado Britta Simon é criado no Screencast-O-Matic. O Screencast-O-Matic suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Screencast-O-Matic, um novo é criado após a autenticação. Se necessitar de criar um utilizador manualmente, contacte a [equipa de suporte do Cliente Screencast-O-Matic](mailto:support@screencast-o-matic.com).

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Screencast-O-Matic no Painel de Acesso, deverá ser automaticamente inscrito no Screencast-O-Matic para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente screencast-O-Matic com Azure AD](https://aad.portal.azure.com/)