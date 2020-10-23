---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com JOSA Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o JOSA.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/08/2020
ms.author: jeedes
ms.openlocfilehash: 290d70e4dc83a4db0b1c0c31e9d60a32d124dc75
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92459327"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-josa"></a>Tutorial: Azure Ative Directory integração única (SSO) com JOSA

Neste tutorial, você vai aprender a integrar JOSA com Azure Ative Directory (Azure AD). Quando integrar o JOSA com a AD Azure, pode:

* Controlo em Azure AD que tem acesso a JOSA.
* Ative os seus utilizadores a serem automaticamente inscritos na JOSA com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Josa assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* JOSA apoia SSO iniciado **pela SP**

## <a name="adding-josa-from-the-gallery"></a>Adicionar JOSA da galeria

Para configurar a integração do JOSA no AD Azure, precisa adicionar o JOSA da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **da galeria,** escreva **JOSA** na caixa de pesquisa.
1. Selecione **JOSA** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-josa"></a>Configurar e testar Azure AD único sinal para JOSA

Configure e teste Azure AD SSO com JOSA usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em JOSA.

Para configurar e testar o Azure AD SSO com o JOSA, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o JOSA SSO](#configure-josa-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create JOSA test user](#create-josa-test-user)** - para ter uma contraparte de B.Simon em JOSA que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **JOSA,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** se tiver **um ficheiro de metadados do Fornecedor de Serviços,** execute os seguintes passos:

    a. Clique **em Carregar o ficheiro de metadados**.

    ![Carregar ficheiro de metadados](common/upload-metadata.png)

    b. Clique no **logotipo da pasta** para selecionar o ficheiro de metadados e clique em **Upload**.

    ![escolher arquivo de metadados](common/browse-upload-metadata.png)

    c. Após o carregamento do ficheiro de metadados com sucesso, o valor **do Identificador** é preenchido automaticamente na secção de Configuração BÁSICA SAML.

    Na caixa de texto **URL de entrada de inscrição,** digite o URL:  `https://www.jo-sa.dk/adfslogin.php`

    > [!Note]
    > Se o valor **do Identificador** não for automaticamente poluído, por favor preencha o valor manualmente de acordo com o seu requisito. O valor url de inscrição não é real. Atualize este valor com o URL de inscrição real. Contacte [a equipa de apoio ao cliente JOSA](mailto:hr@alldialogue.com) para obter este valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a JOSA.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **JOSA**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-josa-sso"></a>Configurar JOSA SSO

Para configurar um único sinal no lado **JOSA,** é necessário enviar o **Url de Metadados da Federação de Aplicações** para a [equipa de suporte JOSA](mailto:hr@alldialogue.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-josa-test-user"></a>Criar utilizador de teste JOSA

Nesta secção, cria-se um utilizador chamado B.Simon em JOSA. Trabalhe com a [equipa de apoio JOSA](mailto:hr@alldialogue.com) para adicionar os utilizadores na plataforma JOSA. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo JOSA no Painel de Acesso, deverá ser automaticamente inscrito no JOSA para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente JOSA com Azure AD](https://aad.portal.azure.com/)