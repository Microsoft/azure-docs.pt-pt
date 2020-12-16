---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com FloQast Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o FloQast.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/10/2019
ms.author: jeedes
ms.openlocfilehash: ed35a0b6e6f2ad620b528283a332507845e4f00d
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97588306"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-floqast"></a>Tutorial: Azure Ative Directory integração única (SSO) com FloQast

Neste tutorial, você vai aprender a integrar FloQast com Azure Ative Directory (Azure AD). Quando integrar o FloQast com a AD Azure, pode:

* Controle em Azure AD que tem acesso ao FloQast.
* Ative os seus utilizadores a serem automaticamente inscritos no FloQast com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição única ativada por FloQast (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* FloQast apoia **SP e IDP** iniciado SSO

## <a name="adding-floqast-from-the-gallery"></a>Adicionando FloQast da galeria

Para configurar a integração do FloQast no AD Azure, precisa adicionar o FloQast da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **da galeria,** **digite FloQast** na caixa de pesquisa.
1. Selecione **FloQast** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-floqast"></a>Configurar e testar Azure AD único sinal para FloQast

Configure e teste Azure AD SSO com FloQast usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em FloQast.

Para configurar e testar o Azure AD SSO com o FloQast, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure floQast SSO](#configure-floqast-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create FloQast test user](#create-floqast-test-user)** - para ter uma contraparte de B.Simon em FloQast que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **FloQast,** encontre a secção **Gerir** e selecione **um único sinal de sing.**
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    Na caixa de texto **identifier,** digite um URL:  `https://go.floqast.com/`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL:  `https://go.floqast.com/login/sso`

1. A aplicação FloQast espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação FloQast espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Nome | Atributo de origem|
    | ------------- | -------------- |
    | FirstName | user.givenname |
    | LastName | utilizador.sobrenome |
    | E-mail | user.mail    |

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

9. Na secção **Certificado de Assinatura SAML,** clique no botão **Editar** para abrir o diálogo **do Certificado de Assinatura SAML** e efetue o passo seguinte.

    ![Editar certificado de assinatura SAML](common/edit-certificate.png)

    1. Selecione **Sign SAML resposta e afirmação** a partir da **Opção de Assinatura**.

    1. Clique em **Guardar**

        ![Opção de assinatura de communifire](./media/floqast-tutorial/tutorial-floqast-signing-option.png)

1. Na secção **Configurar FloQast,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao FloQast.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **FloQast**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-floqast-sso"></a>Configure FloQast SSO

Para configurar um único sign-on no lado **FloQast,** você precisa enviar o **metdata XML da Federação** descarregado e URLs copiados apropriados do portal Azure para a equipe de [suporte FloQast](mailto:support@floqast.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-floqast-test-user"></a>Criar utilizador de teste FloQast

Nesta secção, cria-se um utilizador chamado B.Simon in FloQast. Trabalhe com a [equipa de suporte floQast](mailto:support@floqast.com) para adicionar os utilizadores na plataforma FloQast. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo FloQast no Painel de Acesso, deverá ser automaticamente inscrito no FloQast para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente FloQast com Azure AD](https://aad.portal.azure.com/)