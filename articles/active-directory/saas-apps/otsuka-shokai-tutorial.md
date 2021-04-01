---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com Otsuka Shokai | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Otsuka Shokai.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/02/2020
ms.author: jeedes
ms.openlocfilehash: ef2151c7f3c03ff8996bae1814e4df638b25490f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92513743"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-otsuka-shokai"></a>Tutorial: Azure Ative Directory single sign-on (SSO) integração com Otsuka Shokai

Neste tutorial, você vai aprender a integrar Otsuka Shokai com Azure Ative Directory (Azure AD). Quando integrar o Otsuka Shokai com Azure AD, pode:

* Controlo em Azure AD que tem acesso a Otsuka Shokai.
* Permita que os seus utilizadores sejam automaticamente inscritos na Otsuka Shokai com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Otsuka Shokai single sign-on (SSO) ativou a subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Otsuka Shokai apoia **IDP** iniciado SSO

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="adding-otsuka-shokai-from-the-gallery"></a>Adicionando Otsuka Shokai da galeria

Para configurar a integração de Otsuka Shokai em Azure AD, você precisa adicionar Otsuka Shokai da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add da secção **da galeria,** **digite Otsuka Shokai** na caixa de pesquisa.
1. Selecione **Otsuka Shokai** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-otsuka-shokai"></a>Configurar e testar Azure AD único sign-on para Otsuka Shokai

Configure e teste Azure AD SSO com Otsuka Shokai usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em Otsuka Shokai.

Para configurar e testar a Azure AD SSO com o Otsuka Shokai, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Otsuka Shokai SSO](#configure-otsuka-shokai-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Otsuka Shokai test user](#create-otsuka-shokai-test-user)** - para ter uma contraparte de B.Simon em Otsuka Shokai que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Otsuka Shokai,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** a aplicação está pré-configurada no modo iniciado pelo **IDP** e os URLs necessários já estão pré-povoados com Azure. O utilizador precisa de guardar a configuração clicando no botão **Guardar.**

1. A aplicação Otsuka Shokai espera as afirmações DO SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem seguinte mostra a lista de atributos predefinidos, onde como **identificador** de nome é mapeado com **user.userprincipalname**. A aplicação Otsuka Shokai espera que **o identificador** de nomes seja mapeado com **user.objectid**, pelo que é necessário editar o mapeamento do atributo clicando no ícone **Editar** e alterar o mapeamento do atributo.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação PureCloud by Genesys espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Name | Atributo de origem|
    | ---------------| --------------- |
    | Appid | `<Application ID>` |

    >[!NOTE]
    >`<Application ID>` é o valor que copiou a partir do **separador Propriedades** do portal Azure.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Otsuka Shokai.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Otsuka Shokai**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-otsuka-shokai-sso"></a>Configure Otsuka Shokai SSO

1. Quando se conecta à Minha Página do Cliente a partir da aplicação SSO, o assistente de definição SSO começa.

2. Se o ID de Otsuka não estiver registado, dirija-se ao novo registo de identificação da Otsuka.ID.   Se tiver registado o Otsuka-ID, dirija-se à definição de ligação.

3. Proceda ao fim e quando o ecrã superior for apresentado depois de iniciar sessão na Página Minha do Cliente, as definições SSO estão completas.

4. Da próxima vez que ligar à Minha Página do Cliente a partir da aplicação SSO, após a abertura do ecrã de orientação, o ecrã superior é apresentado depois de iniciar sessão na Minha Página do Cliente.

### <a name="create-otsuka-shokai-test-user"></a>Criar utilizador de teste Otsuka Shokai

O novo registo da conta SaaS será realizado no primeiro acesso a Otsuka Shokai. Além disso, também associaremos a conta Azure AD e a conta SaaS no momento da nova criação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Otsuka Shokai no Painel de Acesso, deverá ser automaticamente inscrito no Otsuka Shokai para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente Otsuka Shokai com Azure AD](https://aad.portal.azure.com/)