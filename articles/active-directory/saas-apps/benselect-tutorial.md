---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com o BenSelect / Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o BenSelect.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: jeedes
ms.openlocfilehash: 714e293220317e46113c4ffdda2e207da62be6c1
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92457253"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-benselect"></a>Tutorial: Azure Ative Directory integração única (SSO) com BenSelect

Neste tutorial, você vai aprender a integrar o BenSelect com o Azure Ative Directory (Azure AD). Quando integrar o BenSelect com a AD Azure, pode:

* Controlo em Azure AD que tem acesso ao BenSelect.
* Permita que os seus utilizadores sejam automaticamente inscritos no BenSelect com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* BenSelect assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.


* BenSelect apoia **IDP** iniciado SSO

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="adding-benselect-from-the-gallery"></a>Adicionar BenSelect da galeria

Para configurar a integração do BenSelect no AZure AD, é necessário adicionar o BenSelect da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **da galeria,** **digitelecte BenSelect** na caixa de pesquisa.
1. Selecione **BenSelect** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-benselect"></a>Configurar e testar Azure AD único sinal para BenSelect

Configure e teste Azure AD SSO com BenSelect usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no BenSelect.

Para configurar e testar o Azure AD SSO com o BenSelect, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o BenSelect SSO](#configure-benselect-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create BenSelect test user](#create-benselect-test-user)** - para ter uma contraparte de B.Simon em BenSelect que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **BenSelect,** encontre a secção **Gerir** e selecione **um único sinal**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:  `https://www.benselect.com/enroll/login.aspx?Path=<tenant name>`

    > [!NOTE]
    > O valor não é real. Atualizar o valor com o URL de resposta real. Contacte [a equipa de apoio ao Cliente BenSelect](mailto:support@selerix.com) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação BenSelect espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![A screenshot mostra atributos do utilizador com atributos predefinidos, tais como dado o nome de utilizador.givenname e e-mail.mail.](common/edit-attribute.png)

1. Clique no ícone **Editar** para editar o valor do **identificador nome**.

    ![A screenshot mostra o painel de atributos do utilizador & Claims com o ícone editar chamado.](media/benselect-tutorial/mail-prefix1.png)

1. Na secção **Gerir reclamações do utilizador,** execute os seguintes passos:

    ![Screenshot mostra Gerir as alegações do utilizador onde pode introduzir os valores descritos neste passo.](media/benselect-tutorial/mail-prefix2.png)

    a. Selecione **a Transformação** como **Fonte**.

    b. Na lista de abandonos de **transformação,** selecione **ExtractMailPrefix()**.

    c. Na lista de retirada do **parâmetro 1,** selecione **user.userprincipalname**.

    d. Clique em **Guardar**.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o Certificado **(Raw)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificateraw.png)

1. Na secção **set up BenSelect,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao BenSelect.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **BenSelect**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-benselect-sso"></a>Configure BenSelect SSO

Para configurar um único sign-on no lado **benSelect,** você precisa enviar o Certificado descarregado **(Raw)** e URLs copiados apropriados do portal Azure para a equipe de [suporte BenSelect](mailto:support@selerix.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

> [!NOTE]
> É necessário mencionar que esta integração requer que o algoritmo SHA256 (SHA1 não é suportado) para definir o SSO no servidor apropriado, como app2101, etc.

### <a name="create-benselect-test-user"></a>Criar utilizador de teste BenSelect

Nesta secção, cria-se um utilizador chamado Britta Simon em BenSelect. Trabalhe com a [equipa de suporte benSelect](mailto:support@selerix.com) para adicionar os utilizadores na plataforma BenSelect. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo BenSelect no Painel de Acesso, deverá ser automaticamente inscrito no BenSelect para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o BenSelect com a Azure AD](https://aad.portal.azure.com/)