---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com a Plataforma de Gestão de Propriedades yuhu | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Plataforma de Gestão de Propriedades Yuhu.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2019
ms.author: jeedes
ms.openlocfilehash: 2b5e852e3a32c4fb40dbe7ebc50a00302b4b82b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92519103"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-yuhu-property-management-platform"></a>Tutorial: Azure Ative Directory integração única (SSO) com a Plataforma de Gestão de Propriedades yuhu

Neste tutorial, você vai aprender a integrar a Plataforma de Gestão de Propriedades Yuhu com o Azure Ative Directory (Azure AD). Quando integrar a Plataforma de Gestão de Propriedades yuhu com a Ad Azure, pode:

* Controle em Azure AD que tem acesso à Plataforma de Gestão de Propriedades yuhu.
* Permita que os seus utilizadores sejam automaticamente inscritos na Plataforma de Gestão de Propriedades yuhu com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Yuhu Property Management Platform única subscrição ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Plataforma de Gestão de Propriedades Yuhu suporta **SSO** iniciado sp

## <a name="adding-yuhu-property-management-platform-from-the-gallery"></a>Adicionar a Plataforma de Gestão de Propriedades Yuhu da galeria

Para configurar a integração da Plataforma de Gestão de Propriedades yuhu em AD Azure, você precisa adicionar a Plataforma de Gestão de Propriedades Yuhu da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add a partir da secção **de galeria,** escreva plataforma de **gestão de propriedade yuhu** na caixa de pesquisa.
1. Selecione a Plataforma de Gestão de **Propriedades Yuhu** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-yuhu-property-management-platform"></a>Configurar e testar a Azure AD um único sign-on para a Plataforma de Gestão de Propriedades yuhu

Configure e teste Azure AD SSO com a Yuhu Property Management Platform usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na Plataforma de Gestão de Propriedades yuhu.

Para configurar e testar o Azure AD SSO com a Plataforma de Gestão de Propriedades yuhu, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure a Plataforma de Gestão de Propriedades da Yuhu SSO](#configure-yuhu-property-management-platform-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create Yuhu Property Management Platform test user](#create-yuhu-property-management-platform-test-user)** - para ter uma contrapartida de B.Simon na Plataforma de Gestão de Propriedades Yuhu que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações da **Plataforma de Gestão de Propriedades yuhu,** encontre a secção **Gerir** e selecione um **único sinal.**
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.yuhu.io/companies`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `yuhu-<ID>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de suporte do cliente da Plataforma de Gestão de Propriedades Yuhu](mailto:hello@yuhu.io) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação Yuhu Property Management Platform espera as afirmações DO SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação da Yuhu Property Management Platform espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Name | Atributo de origem|
    | ---------------| --------------- |
    | nomePróprio | user.givenname |
    | apelido | utilizador.sobrenome ||
    | e-mail | user.mail |

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o Certificado **(Raw)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificateraw.png)

1. Na secção Configurar a **Plataforma de Gestão de Propriedades Yuhu,** copie os URL(s) apropriados com base na sua exigência.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Plataforma de Gestão de Propriedades yuhu.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Yuhu Property Management Platform**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-yuhu-property-management-platform-sso"></a>Configure a plataforma de gestão de propriedades yuhu SSO

Para configurar um único sign-on no lado da Plataforma de **Gestão de Propriedades yuhu,** você precisa enviar o Certificado descarregado **(Raw)** e URLs copiados apropriados do portal Azure para a equipe de [suporte da Plataforma de Gestão de Propriedades Yuhu.](mailto:hello@yuhu.io) Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-yuhu-property-management-platform-test-user"></a>Criar o utilizador de teste da Plataforma de Gestão de Propriedades Yuhu

Nesta secção, cria-se um utilizador chamado B.Simon na Plataforma de Gestão de Propriedades yuhu. Trabalhe com a [equipa de suporte da Plataforma de Gestão de Propriedades yuhu](mailto:hello@yuhu.io) para adicionar os utilizadores na plataforma yuhu Property Management Platform. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo da Plataforma de Gestão de Propriedades Yuhu no Painel de Acesso, deverá ser automaticamente inscrito na Plataforma de Gestão de Propriedades yuhu para a qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente a Plataforma de Gestão de Propriedades yuhu com Azure AD](https://aad.portal.azure.com/)