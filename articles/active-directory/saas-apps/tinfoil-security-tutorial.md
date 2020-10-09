---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com a TINFOIL SECURITY Microsoft Docs'
description: Saiba como configurar um único sinal de inscrição entre o Azure Ative Directory e o TINFOIL SECURITY.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: jeedes
ms.openlocfilehash: 35ff822b4439a48a922fe5ccd4530dfb5d40ffae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88552040"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tinfoil-security"></a>Tutorial: Azure Ative Directory integração única (SSO) com SEGURANÇA TINFOIL

Neste tutorial, você vai aprender a integrar a SEGURANÇA TINFOIL com o Azure Ative Directory (Azure AD). Quando integrar a TINFOIL SECURITY com a Ad Azure, pode:

* Controlo em Azure AD que tem acesso à SEGURANÇA TINFOIL.
* Permita que os seus utilizadores sejam automaticamente inscritos na TINFOIL SECURITY com as suas contas AD AZure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* TINFOIL SEGURANÇA única assinatura ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* TINFOIL SECURITY suporta **IDP** iniciado SSO

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="adding-tinfoil-security-from-the-gallery"></a>Adicionando SEGURANÇA TINFOIL da galeria

Para configurar a integração da TINFOIL SECURITY no Azure AD, é necessário adicionar a SEGURANÇA TINFOIL da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** escreva **TINFOIL SECURITY** na caixa de pesquisa.
1. Selecione **TINFOIL SECURITY** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-tinfoil-security"></a>Configurar e testar Azure AD único sinal de segurança para a SEGURANÇA TINFOIL

Configure e teste Azure AD SSO com TINFOIL SECURITY utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em TINFOIL SECURITY.

Para configurar e testar o Azure AD SSO com SEGURANÇA TINFOIL, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o SSO DE SEGURANÇA TINFOIL](#configure-tinfoil-security-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create TINFOIL SECURITY test user](#create-tinfoil-security-test-user)** - para ter uma contraparte de B.Simon em TINFOIL SECURITY que está ligada à representação AZure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **TINFOIL SECURITY,** encontre a secção **Gerir** e selecione um único sinal de **sing.**
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** a aplicação está pré-configurada e os URLs necessários já estão pré-povoados com Azure. O utilizador precisa de guardar a configuração clicando no botão **Guardar.**

1. A aplicação visitly espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação Visitly espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Nome | Atributo de origem |
    | ------------------- | -------------|
    | accountid | UXXXXXXXXXXXXXXXX |

    > [!NOTE]
    > Receberá o valor contabilístico explicado mais tarde no tutorial.

1. Na secção **Certificado de Assinatura SAML,** clique em Editar o botão **Editar** para abrir o diálogo **do Certificado de Assinatura SAML.**

    ![Editar certificado de assinatura SAML](common/edit-certificate.png)

1. Na secção **Certificado de Assinatura SAML,** copie o **Valor de Impressão Digital** e guarde-o no seu computador.

    ![Valor da impressão digital do polegar da cópia](common/copy-thumbprint.png)

1. Na secção Configurar a SECÇÃO **SEGURANÇA TINFOIL,** copie os URL(s) apropriados com base na sua exigência.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à TINFOIL SECURITY.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **TINFOIL SECURITY**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-tinfoil-security-sso"></a>Configurar a SEGURANÇA DE TINFOIL SSO

1. Numa janela diferente do navegador web, inscreva-se no site da empresa TINFOIL SECURITY como administrador.

1. Na barra de ferramentas em cima, clique em **"Minha Conta".**

    ![Dashboard](./media/tinfoil-security-tutorial/ic798971.png "Dashboard")

1. Clique **em Segurança**.

    ![Segurança](./media/tinfoil-security-tutorial/ic798972.png "Segurança")

1. Na página de configuração **'Sign-On' único,** execute os seguintes passos:

    ![Único sign-on](./media/tinfoil-security-tutorial/ic798973.png "Início de Sessão Único")

    a. Selecione **Ativar SAML**.

    b. Clique na **configuração manual**.

    c. Na caixa de texto **URL DO SAML Post,** cole o valor do URL de **login** que copiou do portal Azure

    d. Na caixa de texto **de impressão digital do certificado SAML,** cole o valor da impressão **digital** thumbprint que copiou da secção certificado de **assinatura SAML.**
  
    e. Copie o valor **do ID da sua conta** e cole o valor na caixa de texto **'Atributos de Origem'** na secção **Atributos & Do Utilizador** no portal Azure.

    f. Clique em **Guardar**.

### <a name="create-tinfoil-security-test-user"></a>Criar utilizador de teste de SEGURANÇA TINFOIL

Para permitir que os utilizadores da Azure AD inscrevam-se na TINFOIL SECURITY, devem ser a provisionados na SEGURANÇA DE TINFOIL. No caso de SEGURANÇA TINFOIL, o provisionamento é uma tarefa manual.

**Para obter um utilizador a provisionado, execute os seguintes passos:**

1. Se o utilizador fizer parte de uma conta Enterprise, tem de contactar a equipa de [suporte da TINFOIL SECURITY](https://www.tinfoilsecurity.com/contact) para criar a conta de utilizador.

1. Se o utilizador for um utilizador regular do TINFOIL SECURITY SaaS, então o utilizador pode adicionar um colaborador a qualquer um dos sites do utilizador. Isto desencadeia um processo para enviar um convite para o e-mail especificado para criar uma nova conta de utilizador DE SEGURANÇA TINFOIL.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador DA TINFOIL SECURITY ou APIs fornecidas pela TINFOIL SECURITY para fornecer contas de utilizador Azure AD.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo DE SEGURANÇA TINFOIL no Painel de Acesso, deverá ser automaticamente inscrito na GARANTIA DE TINFOIL para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente a SEGURANÇA TINFOIL com Azure AD](https://aad.portal.azure.com/)