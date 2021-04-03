---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com Coralogix | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Coralogix.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.openlocfilehash: e676268abb9e1a4a597ef431ea6ba6593411711e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92455226"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-coralogix"></a>Tutorial: Azure Ative Directory integração única (SSO) com Coralogix

Neste tutorial, você vai aprender a integrar Coralogix com Azure Ative Directory (Azure AD). Quando integrar coralogix com Azure AD, pode:

* Controlo em Azure AD que tem acesso a Coralogix.
* Ative os seus utilizadores a serem automaticamente inscritos na Coralogix com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição ativada pela Coralogix (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Coralogix suporta **SSO** iniciado sp

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="adding-coralogix-from-the-gallery"></a>Adicionar Coralogix da galeria

Para configurar a integração da Coralogix no AZure AD, é necessário adicionar a Coralogix da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **da galeria,** **digite Coralogix** na caixa de pesquisa.
1. Selecione **Coralogix** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-coralogix"></a>Configurar e testar Azure AD único sinal para Coralogix

Configure e teste Azure AD SSO com Coralogix usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em Coralogix.

Para configurar e testar o Azure AD SSO com coralogix, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure Coralogix SSO](#configure-coralogix-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Coralogix test user](#create-coralogix-test-user)** - para ter uma contraparte de B.Simon em Coralogix que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Coralogix,** encontre a secção **Gerir** e selecione um único sinal de **sing.**
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na **placar na** caixa URL, introduza um URL com o seguinte padrão: `https://<SUBDOMAIN>.coralogix.com`

    b. Na caixa de texto **identifier (Entity ID),** introduza um URL, como:
    
    `https://api.coralogix.com/saml/metadata.xml`

    ou

    `https://aws-client-prod.coralogix.com/saml/metadata.xml` 

    > [!NOTE]
    > O valor de URL de inscrição não é real. Atualize o valor com o URL de inscrição real. Contacte a equipa de [apoio ao Cliente Coralogix](mailto:info@coralogix.com) para obter o valor. Também pode consultar os padrões na secção **configuração básica do SAML** no portal Azure.

 1. A aplicação Coralogix espera as afirmações DOL num formato específico. Configure os seguintes pedidos para este pedido. Pode gerir os valores destes atributos a partir da secção **Atributos** do Utilizador na página de integração da aplicação. Na **configuração single Sign-On com** a página SAML, selecione o botão **Editar** para abrir a caixa de diálogo atributos do **utilizador.**

    ![Screenshot que mostra o diálogo "Atributos do Utilizador" com o botão "Editar" realçado.](common/edit-attribute.png)

1. Na secção **'Reclamações** de Utilizador' na caixa de diálogo **'Atributos do Utilizador',** edite as reclamações utilizando o ícone **Editar.** Também pode adicionar as reclamações utilizando **adicionar uma nova alegação** para configurar o atributoken SAML, tal como mostrado na imagem anterior. Em seguida, tome os seguintes passos:
    
    a. Selecione o **ícone Editar** para abrir a caixa de diálogo **de reclamações** do utilizador Gerir.

    ![Screenshot que mostra o diálogo "User Awards & Claims" com o botão "Editar" realçado.](./media/coralogix-tutorial/tutorial_usermail.png)
    ![imagem](./media/coralogix-tutorial/tutorial_usermailedit.png)

    b. Na lista de **identificadores de nomes Escolha,** selecione **o endereço de e-mail.**

    c. Na lista **de atributos Source,** selecione **user.mail**.

    d. Selecione **Guardar**.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configurar Coralogix,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Coralogix.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Coralogix**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-coralogix-sso"></a>Configure Coralogix SSO

Para configurar um único sign-on no lado **coralogix,** você precisa enviar o **metdata XML da Federação** descarregado e URLs copiados apropriados do portal Azure para a equipa de [suporte coralogix](mailto:info@coralogix.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-coralogix-test-user"></a>Criar utilizador de teste Coralogix

Nesta secção, cria-se um utilizador chamado Britta Simon em Coralogix. Trabalhe com a [equipa de suporte da Coralogix](mailto:info@coralogix.com) para adicionar os utilizadores na plataforma Coralogix. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Coralogix no Painel de Acesso, deverá ser automaticamente inscrito no Coralogix para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente Coralogix com Azure AD](https://aad.portal.azure.com/)