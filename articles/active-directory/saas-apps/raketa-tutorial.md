---
title: 'Tutorial: Azure Ative Directory integração única (SSO) com Raketa | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Raketa.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/28/2020
ms.author: jeedes
ms.openlocfilehash: 649a26e474c7c4d6b4f51a035b3f8f9da35b9dd1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92511159"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-raketa"></a>Tutorial: Azure Ative Directory integração única (SSO) com Raketa

Neste tutorial, você vai aprender a integrar Raketa com Azure Ative Directory (Azure AD). Quando integrar raketa com Azure AD, você pode:

* Controlo em Azure AD que tem acesso a Raketa.
* Permita que os seus utilizadores sejam automaticamente inscritos no Raketa com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada por Raketa (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Raketa suporta **SP** iniciado SSO.
* Uma vez configurado Raketa, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-raketa-from-the-gallery"></a>Adicionar Raketa da galeria

Para configurar a integração de Raketa em Azure AD, você precisa adicionar Raketa da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory** [1].

    ![rkt_1](./media/raketa-tutorial/azure-active-directory.png)

1. Navegue para **aplicações empresariais** [2] e, em seguida, selecione **Todas as Aplicações** [3].

1. Para adicionar nova aplicação, selecione **Nova aplicação** [4]. 

    ![rkt_2](./media/raketa-tutorial/new-app.png)

1. Na secção **Adicionar da galeria** [5], **digite Raketa** na caixa de pesquisa [6].

1. Selecione **Raketa** do painel de resultados [7] e, em seguida, clique no botão **Adicionar** [8]. 

    ![rkt_3](./media/raketa-tutorial/add-btn.png)


## <a name="configure-and-test-azure-ad-single-sign-on-for-raketa"></a>Configurar e testar Azure AD único sinal para Raketa

Configure e teste Azure AD SSO com Raketa usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em Raketa.

Para configurar e testar o Azure AD SSO com raketa, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Raketa SSO](#configure-raketa-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Raketa test user](#create-raketa-test-user)** - para ter uma contraparte de B.Simon em Raketa que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Raketa,** encontre a secção **Gerir** e selecione **um único sinal de sação** [9].

    ![rkt_4](./media/raketa-tutorial/manage-sso.png)

1. Na página **de método de inscrição** única [9], selecione **SAML** [10].

    ![rkt_5](./media/raketa-tutorial/saml.png)

1. No **set up single-on com** a página SAML, clique no ícone edit/pen para **Configuração BÁSICA SAML** [11] para editar as definições.

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    1. Nas caixas de texto **do Identificador (12] [12]** e **do Signo em URL** [14], digite o URL: `https://raketa.travel/` .

    1. Na caixa de texto **URL de resposta** [13], digite um URL utilizando o seguinte padrão: `https://raketa.travel/sso/acs?clientId=<CLIENT_ID>` .  

    ![rkt_6](./media/raketa-tutorial/enter-urls.png)

    > [!NOTE]
    > O valor URL de resposta não é real. Atualizar o valor com o URL de resposta real. Contacte [a equipa de apoio ao Cliente Raketa](mailto:help@raketa.travel) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o Certificado **(Base64)** e selecione **Download** [15] para descarregar o certificado e guardá-lo no seu computador.

1. Na secção **Configurar Raketa,** copie os URL(s) apropriados com base no seu requisito.

    1. URL de login [16] – O URL de página web de autorização, que é usado para redirecionar os utilizadores para o sistema de autenticação.

    1. Azure Ad Identifier [17] – Identificador Azure Ad.

    1. URL logout [18] – O URL de página web, que é usado para redirecionar os utilizadores após o início de uta.

    ![rkt_7](./media/raketa-tutorial/copy-urls.png)


### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **O Diretório Ativo Azure** [1], selecione **Utilizadores** [19], e, em seguida, selecione **Todos os utilizadores** [20].

1. Selecione **Novo utilizador** [21] na parte superior do ecrã.

    ![rkt_8](./media/raketa-tutorial/new-user.png)

1. Nas propriedades do **Utilizador,** siga estes passos:

   1. No campo **nome do utilizador** [22], insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.

   1. No campo **Nome** [23], insira. `B.Simon`

   1. Selecione a caixa **de verificação de senha show** [25], e, em seguida, anota o valor que é apresentado na caixa **palavra-passe** [24].

   1. Clique em **Criar** [26]. 

    ![rkt_9](./media/raketa-tutorial/create-user.png)


### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Raketa.

1. No portal Azure, selecione **Aplicações empresariais** [2], e, em seguida, selecione **Todas as aplicações** [3].

1. Na lista de candidaturas, selecione **Raketa** [27].  

    ![rkt_10](./media/raketa-tutorial/add-raketa.png)

1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos** [28]. 

    ![rkt_11](./media/raketa-tutorial/users-groups.png)

1. **Selecione Adicionar utilizador** [29], selecione **Utilizadores e grupos** [30] no diálogo **'Adicionar Atribuição'.**

    ![rkt_12](./media/raketa-tutorial/add-user-raketa.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** [31] da lista de Utilizadores e, em seguida, clique no botão **Select** [32] na parte inferior do ecrã.

1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

1. No diálogo **'Adicionar Atribuição',** clique no botão **Atribuir** [33]. 

    ![rkt_13](./media/raketa-tutorial/assign-user.png)

## <a name="configure-raketa-sso"></a>Configurar Raketa SSO

Para configurar um único sign-on no lado **raketa,** você precisa enviar o Certificado descarregado **(Base64)** e URLs copiados apropriados do portal Azure para a equipa de [suporte raketa](mailto:help@raketa.travel). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-raketa-test-user"></a>Criar utilizador de teste Raketa

Nesta secção, cria-se um utilizador chamado B.Simon em Raketa. Trabalhe com a [equipa de apoio da Raketa](mailto:help@raketa.travel) para adicionar os utilizadores na plataforma Raketa. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo raketa no Painel de Acesso, deverá ser automaticamente inscrito no Raketa para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente Raketa com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger Raketa com visibilidade e controlos avançados](/cloud-app-security/proxy-intro-aad)