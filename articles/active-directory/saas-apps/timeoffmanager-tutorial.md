---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com TimeOffManager | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o TimeOffManager.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/10/2019
ms.author: jeedes
ms.openlocfilehash: 849236b9ac33cec92cc145bb32b4271b73476057
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97608821"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-timeoffmanager"></a>Tutorial: Azure Ative Directory integração única (SSO) com TimeOffManager

Neste tutorial, você vai aprender a integrar TimeOffManager com Azure Ative Directory (Azure AD). Quando integrar o TimeOffManager com a Azure AD, pode:

* Controlo em Azure AD que tem acesso ao TimeOffManager.
* Capacitar os seus utilizadores a serem automaticamente inscritos no TimeOffManager com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* TimeOffManager única subscrição (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.


* TimeOffManager apoia **IDP** iniciado SSO

* TimeOffManager suporta o fornecimento **de utilizadores just in time**

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.


## <a name="adding-timeoffmanager-from-the-gallery"></a>Adicionar TimeOffManager da galeria

Para configurar a integração do TimeOffManager no AD Azure, é necessário adicionar o TimeOffManager da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **escreva TimeOffManager** na caixa de pesquisa.
1. Selecione **TimeOffManager** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-timeoffmanager"></a>Configurar e testar Azure AD único sinal para TimeOffManager

Configure e teste Azure AD SSO com TimeOffManager usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no TimeOffManager.

Para configurar e testar a Azure AD SSO com timeOffManager, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure TimeOffManager SSO](#configure-timeoffmanager-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create TimeOffManager test user](#create-timeoffmanager-test-user)** - para ter uma contraparte de B.Simon em TimeOffManager que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **TimeOffManager,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:  `https://www.timeoffmanager.com/cpanel/sso/consume.aspx?company_id=<companyid>`

    > [!NOTE]
    > Este valor não é real. Atualize este valor com o URL de resposta real. Pode obter este valor a partir de **Um Único Sinal na página de definições** que é explicado mais tarde na equipa de suporte tutorial ou Contact [TimeOffManager](https://www.purelyhr.com/contact-us). Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação TimeOffManager espera as afirmações DO SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/edit-attribute.png)

1. Além de acima, a aplicação TimeOffManager espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com o seu requisito.

    | Name | Atributo de origem|
    | --- | --- |
    | Primeiro nome |Nome de utilizador.given |
    | Último nome |User.sobrenome |
    | E-mail |User.mail |

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **set up TimeOffManager,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao TimeOffManager.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **TimeOffManager**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-timeoffmanager-sso&quot;></a>Configurar TimeOffManager SSO

1. Numa janela diferente do navegador web, inscreva-se no site da empresa TimeOffManager como administrador.

2. Ir para **\> opções de conta \> configurações Sign-On únicas**.
   
    ![A screenshot mostra as definições de Sign-On única selecionadas a partir de opções de conta.](./media/timeoffmanager-tutorial/ic795917.png &quot;Definições de Sign-On única")

3. Na secção **Definições de Sign-On Única,** execute os seguintes passos:
   
    ![A screenshot mostra a secção Definições de Sign-On única onde pode introduzir os valores descritos.](./media/timeoffmanager-tutorial/ic795918.png "Definições de Sign-On única")
   
    a. Abra o certificado codificado base-64 no bloco de notas, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole todo o Certificado na caixa de texto **do Certificado X.509.**
   
    b. Na caixa de texto **do Idp Emitente,** cole o valor do **Identificador AD AZure** que copiou do portal Azure.
   
    c. Na caixa de texto **IdP Endpoint URL,** cole o valor do URL de **login** que copiou do portal Azure.
   
    d. Como **Enforce SAML**, selecione **Nº**.
   
    e. Como **Utilizadores de Criação Automática,** selecione **Sim**.
   
    f. Na caixa **de textos LOGOUT URL,** cole o valor do **URL logout** que copiou do portal Azure.
   
    exemplo, clique em **Guardar Alterações**.

4. Na página **'Sign in Settings',** copie o valor do URL do Serviço ao Consumidor de **Afirmação** e cole-o na caixa de texto **URL de resposta** na secção de **Configuração BÁSICA SAML** no portal Azure. 

    ![A screenshot mostra a ligação Com o Serviço de Consumo de Afirmação U R L.](./media/timeoffmanager-tutorial/ic795915.png "Definições de Sign-On única")

### <a name="create-timeoffmanager-test-user"></a>Criar utilizador de teste TimeOffManager

Nesta secção, um utilizador chamado Britta Simon é criado no TimeOffManager. O TimeOffManager suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no TimeOffManager, um novo é criado após a autenticação.

>[!NOTE]
>Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador TimeOffManager ou APIs fornecidas pelo TimeOffManager para fornecer contas de utilizador Azure AD.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo TimeOffManager no Painel de Acesso, deverá ser automaticamente inscrito no TimeOffManager para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente TimeOffManager com Azure AD](https://aad.portal.azure.com/)