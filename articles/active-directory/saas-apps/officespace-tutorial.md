---
title: 'Tutorial: Azure Ative Directory integração única (SSO) com o OfficeSpace Software Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o OfficeSpace Software.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: jeedes
ms.openlocfilehash: ae8a1fa68e61e160ce08b93bf66776a050b45613
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554188"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-officespace-software"></a>Tutorial: Azure Ative Directory integração única (SSO) com software OfficeSpace

Neste tutorial, você vai aprender a integrar o OfficeSpace Software com a Azure Ative Directory (Azure AD). Quando integrar o OfficeSpace Software com AZure AD, pode:

* Controle em Azure AD que tem acesso ao OfficeSpace Software.
* Capacitar os seus utilizadores a serem automaticamente inscritos no OfficeSpace Software com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição ativada por Software OfficeSpace (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* OfficeSpace Software suporta **SSO** iniciado SP


* OfficeSpace Software suporta **provisão de** utilizadores just in time


## <a name="adding-officespace-software-from-the-gallery"></a>Adicionar Software OfficeSpace da galeria

Para configurar a integração do OfficeSpace Software no Azure AD, é necessário adicionar o Software OfficeSpace da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** digite **Software OfficeSpace** na caixa de pesquisa.
1. Selecione **o Software OfficeSpace** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-officespace-software"></a>Configurar e testar a Azure AD um único sign-on para o OfficeSpace Software

Configure e teste Azure AD SSO com o OfficeSpace Software usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no OfficeSpace Software.

Para configurar e testar o Azure AD SSO com o OfficeSpace Software, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o OfficeSpace Software SSO](#configure-officespace-software-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create OfficeSpace Software test user](#create-officespace-software-test-user)** - para ter uma contraparte de B.Simon no OfficeSpace Software que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **do OfficeSpace Software,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<company name>.officespacesoftware.com/users/sign_in/saml`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `<company name>.officespacesoftware.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de suporte ao cliente do OfficeSpace Software](mailto:support@officespacesoftware.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação OfficeSpace Software espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem seguinte mostra a lista de atributos predefinidos, onde como **identificador** de nome é mapeado com **user.userprincipalname**. A aplicação OfficeSpace Software espera que **o identificador de nomes** seja mapeado com **o user.mail,** pelo que é necessário editar o mapeamento do atributo clicando no ícone **Editar** e alterar o mapeamento do atributo.

    ![image](common/edit-attribute.png)

1. Além de acima, a aplicação OfficeSpace Software espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com o seu requisito.

    | Name | Atributo de origem|
    | ---------------| --------------- |
    | e-mail | user.mail |
    | name | user.displayname |
    | first_name | user.givenname |
    | last_name | utilizador.sobrenome |

1. Na secção **Certificado de Assinatura SAML,** clique em Editar o botão **Editar** para abrir o diálogo **do Certificado de Assinatura SAML.**

    ![Editar certificado de assinatura SAML](common/edit-certificate.png)

1. Na secção **Certificado de Assinatura SAML,** copie o **Valor de Impressão Digital** e guarde-o no seu computador.

    ![Valor da impressão digital do polegar da cópia](common/copy-thumbprint.png)

1. Na secção **De Configuração do Software OfficeSpace,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao OfficeSpace Software.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **OfficeSpace Software**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="configure-officespace-software-sso"></a>Configurar software OfficeSpace SSO

1. Numa janela diferente do navegador web, inscreva-se no seu inquilino do OfficeSpace Software como administrador.

2. Vá a **Definições** e clique em **Conectores**.

    ![Configurar um único sign-on on no lado da aplicação](./media/officespace-tutorial/tutorial_officespace_002.png)

3. Clique na **autenticação SAML**.

    ![Configurar um único sign-on on no lado da aplicação](./media/officespace-tutorial/tutorial_officespace_003.png)

4. Na secção **de Autenticação SAML,** execute os seguintes passos:

    ![Configurar um único sign-on on no lado da aplicação](./media/officespace-tutorial/tutorial_officespace_004.png)

    a. Na url textbox **do fornecedor logout,** cole o valor do **URL logout** que copiou do portal Azure.

    b. Na caixa **de texto do idp target do Cliente,** cole o valor do URL de **Login** que copiou do portal Azure.

    c. Cole o valor **de impressão digital** thumbprint que copiou do portal Azure, para a caixa de texto de impressão digital do certificado de IDP do **Cliente.** 

    d. Clique **em Guardar Definições**.

### <a name="create-officespace-software-test-user"></a>Criar utilizador de teste de Software OfficeSpace

Nesta secção, um utilizador chamado B.Simon é criado no OfficeSpace Software. O OfficeSpace Software suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no OfficeSpace Software, um novo é criado após a autenticação.

> [!NOTE]
> Se precisar de criar um utilizador manualmente, tem de contactar a equipa de [suporte do Software OfficeSpace.](mailto:support@officespacesoftware.com)

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo do Software OfficeSpace no Painel de Acesso, deverá ser automaticamente inscrito no Software OfficeSpace para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente software OfficeSpace com Azure AD](https://aad.portal.azure.com/)

